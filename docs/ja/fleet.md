<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Fleet

FleetのYAMLで依存関係付きの複数エージェント処理を定義し、ライブグラフで監督し、停止や失敗から安全に復旧する方法を説明します。

> AGI Cockpit 4.61.0で2026-08-27に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/fleet)

Fleetは、複数のAIエージェント、コマンドによる検証、人の承認を依存関係グラフとしてYAMLに定義し、一つのRunとして実行する機能です。各エージェントノードは通常のCockpitタスクとして動き、Cockpitが実行順、並列数、待機、再開、履歴を管理します。

単発の仕事を一つだけ別タスクへ渡す場合は`cockpit task run`を使います。依存順や並列処理を繰り返し再現したい場合、途中にテストや承認を置きたい場合、アプリ再起動後も同じ処理を復旧したい場合にFleetを選びます。

## FleetとRunを区別する

| 用語 | 意味 |
| --- | --- |
| Fleet | ノード、依存関係、引数、既定のエージェント設定を記述した再利用可能なYAML |
| Run | そのFleetを一度実行した記録。固有の`runId`、作業場所、イベント履歴を持つ |
| ノード | エージェントタスク、既存タスクへのメッセージ、command gate、human gate、bounded loopのいずれか |
| Fleetパネル | Runの依存グラフ、各ノードの状態、タスク、レポート、イベントを表示するライブ画面 |

Fleetの定義を変更しても、開始済みRunの定義と履歴は書き換わりません。変更後の定義を最初から試す場合は、既存Runの`retry`ではなく`rerun`で新しいRunを作ります。

## 保存場所を選ぶ

| スコープ | 保存場所 | 向いている用途 |
| --- | --- | --- |
| Project | `<repo>/.cockpit/fleets/<name>.yaml` | 一つのリポジトリで繰り返す開発・検証。RunごとにGit Worktreeを作る |
| Global | `~/.agi-tools/data/cockpit/fleets/<name>.yaml` | 複数リポジトリをまたぐ処理、またはリポジトリを必要としない処理 |

Project FleetはCI設定と同じくリポジトリのワークフローです。チームで再利用する定義はコミットします。Global Fleet自体にはリポジトリがないため、必要なノードごとに`directory`を指定します。

## 最小のFleetを作る

次のProject Fleetは、調査、実装、テスト、要約を順に進めます。`defaults`はエージェントノードへ継承され、`needs`が依存関係を作ります。command gateはエージェントを起動せず、コマンドの終了コード0だけを成功として扱います。

`.cockpit/fleets/check-and-fix.yaml`

```yaml
version: 1
name: check-and-fix
description: Investigate one problem, implement the fix, and verify it
args:
  target:
    required: true
    description: What to fix
defaults:
  agent: codex
  effort: medium
max_parallel: 2
nodes:
  investigate:
    prompt: |
      Investigate {{args.target}}. Change nothing.
      Report the cause, affected files, and a concrete fix plan.
  implement:
    needs: [investigate]
    prompt: |
      Implement the fix and verify the focused behavior.
      Investigation: {{needs.investigate.report}}
  tests:
    type: gate
    gate: command
    needs: [implement]
    run: pnpm test
  summarize:
    needs: [tests]
    prompt: |
      Review the completed change and report the result, changed files,
      and verification evidence.
```

`version`は現在`1`だけを使用します。`name`とノードIDには英数字、`_`、`-`を使用できます。未知のキー、存在しない依存先、循環、解決できない展開式、エージェントが対応しない明示設定は検証エラーになります。

## 検証してから実行する

YAMLを保存したら、タスクを一つも作る前に検証します。

```bash
cockpit fleet validate .cockpit/fleets/check-and-fix.yaml --directory /path/to/repo
```

初回は`--max-parallel 1`で順番に動かし、ノードの責務、受け渡すレポート、変更範囲が意図どおりか確認します。問題がなければ並列数を上げます。

```bash
cockpit fleet run check-and-fix \
  --directory /path/to/repo \
  --arg target="search indexing" \
  --max-parallel 1
```

`run`は処理完了を待たず、すぐに`runId`を返します。`run`へ`--wait`を付けず、続けてFleetパネルを開き、必要なときだけ待機します。

```bash
cockpit side-panel fleet <runId>
cockpit fleet wait <runId>
```

`wait`はRunが完了・失敗・停止・一時停止したとき、human gateが回答待ちになったとき、またはノードが中断されたときに返ります。監視プロセスが終了した場合は、直前の`latestSeq`から再開できます。

```bash
cockpit fleet wait <runId> --since <latestSeq>
```

タイムアウトはRunの失敗ではありません。継続監視には`wait`、一回だけ現在値を読む場合は`status`を使い、短い間隔で`status`を繰り返さないでください。

## ライブグラフを読む

Fleetパネルでは、依存関係と次の状態を同時に確認します。

- どのノードが実行中、待機中、完了、失敗、スキップ、除外、承認待ちか
- 並列ブランチのどこまでが終わり、どの依存先が後続を止めているか
- 各エージェントノードが使う実タスク、作業場所、ブランチ、試行回数
- ノードのレポート、構造化出力、command gateの結果、Runイベント

CLIで詳しく確認する場合は次を使います。

| 確認したいこと | コマンド |
| --- | --- |
| 現在状態 | `cockpit fleet status <runId>` |
| 全ノード、解決済みランタイム、branch、task ID | `cockpit fleet show <runId>` |
| Runの判断経緯 | `cockpit fleet logs <runId>` |
| 一つのノードのレポートと失敗理由 | `cockpit fleet logs <runId> --node <nodeId>` |
| ノードの実タスク | `cockpit task get <taskId>` |

実行中ノードのタスクへ直接Steerを送る、turnをキャンセルする、タスクを完了・削除する操作は、そのノードを中断してRunを一時停止させます。意図的に割り込む場合を除き、ノードタスクではなくFleetパネルと`fleet`コマンドから監督します。

## ノードへ役割を割り当てる

エージェントノードには`agent`、`model`、`effort`、`account`、`browser_identity`、`service_tier`、`workspace`、`approval`を設定できます。`defaults`は多数派の設定に使い、違うノードだけ上書きします。実行時の一時的な変更には`--set '<node>.<field>=<value>'`または`--set '*.<field>=<value>'`を使います。

一つのRunでClaudeに設計、Codexに実装、Grokに調査を割り当てられます。ただし、エージェントが対応しない設定をノードへ直接書くと検証に失敗します。`defaults`または`*`から広く継承された非対応設定だけは、そのノードから除外されます。

`max_parallel`はグラフの幅ではなく、同時にアクティブにできるノード数です。既定値は4、範囲は1〜32です。マシンの容量と利用できるエージェントアカウント数に合わせます。

## 結果を後続へ渡す

自由文のレポートは`{{needs.<id>.report}}`、変更ファイルは`{{needs.<id>.files}}`、分離Worktreeのブランチは`{{needs.<id>.branch}}`で参照します。引数は先に`args`へ宣言し、`{{args.<key>}}`で使います。依存関係にない兄弟ノードや後続ノードは参照できません。

機械的な判定へ値を渡す場合は、レポート文を解析せず`output_contract`を使います。JSON Schemaで必須フィールドと型を宣言すると、ノードは完了前に`cockpit fleet output`で値を提出し、Fleetがその場で検証します。

```yaml
  inspect:
    prompt: Count unresolved problems and report the evidence.
    output_contract:
      type: object
      required: [issues]
      properties:
        issues: { type: integer, minimum: 0 }
  clean:
    type: gate
    gate: command
    needs: [inspect]
    run: test "{{needs.inspect.output.issues}}" -eq 0
```

自由文は説明、構造化出力は条件や数値の受け渡しに使い分けます。command gateへエージェントが書いた文字列を直接展開せず、数値・真偽値または`FLEET_NEEDS_<ID>_OUTPUT`として渡される検証済みJSONを使います。

## 並列編集とWorktreeを設計する

Project FleetはRunごとに`fleet/<name>-<runId>`ブランチの共有Worktreeを作ります。`workspace: shared`が既定で、直列ノードは前のノードがディスクへ残した変更を読めます。

同時にコードを編集するノードには`workspace: isolated`を指定します。各ノードは`fleet/<name>-<runId>-<nodeId>`ブランチの別Worktreeで動くため、互いの変更は見えません。分離ノードにはコミットまで依頼し、後続の一つのintegrateノードで`{{needs.<id>.branch}}`を参照してマージします。

Fleetランタイムはブランチを自動マージしません。どの変更を採用し、競合をどう解くかは判断を持つintegrateノードの責務です。並列編集へ`shared`を使うと同じファイルを同時に変更できるため、読み取り専用または明確に分離した範囲だけに限定します。

Global Fleetで`workspace: isolated`を使う場合は、そのノードへリポジトリルートの`directory`が必要です。指定先がGitリポジトリでなければ、通常のフォルダーへ黙って切り替えずノードが失敗します。

## 分岐、展開、反復を使う

| 機能 | 用途 | 重要な挙動 |
| --- | --- | --- |
| `when` | 上流のpass・approvalに基づく実行時分岐 | falseのノードは`skipped`になり、その下流にもskipが伝播する |
| `enabled` | Run引数で高コストな任意ノードを外す | Run作成時に一度評価され、falseは`excluded`。依存順を保ったまま後続は動く |
| `foreach` | 調査で見つけた一覧を一件ずつ並列処理する | 各項目が`max_parallel`を一枠使い、全項目の結果を後続へまとめる |
| bounded loop | 修正と検証を上限回数まで繰り返す | `max_iterations`が無限反復を防ぎ、`until.run`の終了コード0で抜ける |
| message node | Fleet外の既存Cockpitタスクへ指示を送り、そのreportを待つ | 外部タスクを所有せず、Runを停止・削除してもそのタスクは完了・停止しない |

`enabled`を使うFleetは、実行前に同じ引数を付けて`validate`し、どのノードが`excluded`になるか確認します。`foreach`で書き込みを並列化する場合は`workspace: isolated`を使います。loopには必ず現実的な上限を置き、上限到達後にさらに反復すべきかは保存済みの証拠を見て判断します。

## gateで事実と承認を置く

| gate | 成功条件 | 使う場面 |
| --- | --- | --- |
| command | 非対話コマンドが終了コード0で終わる | テスト、lint、型検査、公開状態の機械確認 |
| human | Cockpit Askで利用者が承認する | push、公開、削除、送信、課金など外部に見える、または不可逆な操作の直前 |

command gateは30分でタイムアウトし、同じGitリポジトリのWorktreeを使う別Runのcommand gateとは直列化されます。`retries`は0〜3ですが、自動再試行はVitest形式で1〜2件の失敗テストを特定できる負荷依存flakeだけが対象です。型エラー、ビルド失敗、タイムアウト、同じテストの連続失敗はすぐに失敗として扱います。

human gateのAskは、グラフを見なくても判断できるよう、完了したこと、確認した証拠、承認後に起きる外部操作を本文だけで説明します。却下は下流をskipし、Askを閉じただけの場合はノードが中断されてRunが一時停止します。

## 一時停止と失敗から復旧する

| 状況 | 操作 | 引き継ぐもの |
| --- | --- | --- |
| 新しいノードの開始だけ止めたい | `cockpit fleet pause <runId>` | 実行中ノードは継続する |
| 一時停止、アプリ再起動、中断から続けたい | `cockpit fleet resume <runId>` | 同じRun、同じ作業場所、完了済みノード |
| 一つの失敗原因を直して下流だけやり直したい | `cockpit fleet retry <runId> --node <nodeId>` | 無関係な完了・pass済みノード |
| 定義変更後に最初から試したい | `cockpit fleet rerun <runId>` | 同じFleetと引数。新しい`runId`と作業場所を作る |
| 実行中タスクとgateを止めたい | `cockpit fleet stop <runId>` | 保存済みRunは残り、後で`resume`できる |
| 不要な履歴を完全に消したい | `cockpit fleet remove <runId>` | 何も引き継がない。終端Runだけ削除できる |

失敗ノードの下流はskipされますが、独立したブランチは最後まで動きます。最初に`logs --node`で原因を確認し、実際に誤っている最も上流のノードをretryします。下流だけをretryしても、誤った上流出力は直りません。

アプリ再起動後は、生きているタスクへ再接続し、それ以外の実行中ノードを`interrupted`として保存します。`resume`すると通常のエージェントノードは同じ作業場所の新しいタスクで続くため、各promptは現在のファイルを再確認してから作業できる内容にします。

アカウントの利用上限で中断した場合は、別のサインイン済みprofileへ切り替えて再開できます。

```bash
cockpit fleet resume <runId> --set '*.account=<profile>'
cockpit fleet retry <runId> --node <nodeId> --set '<nodeId>.account=<profile>'
```

Cockpitは直近200件の終端Runを保持します。`remove`はイベント履歴を恒久的に削除するため、必要なレポート、差分、公開URLを保存し、実行中または一時停止中なら先に`stop`してから行います。

## 完了を判定する

FleetパネルでRunが`completed`になったことだけでなく、次を確認します。

- 必須ノードが完了またはpassし、意図しない`skipped`、`excluded`、`interrupted`がない
- 分離ブランチをintegrateノードが取り込み、競合解決とコミットを報告している
- command gateが対象の最終作業場所で成功している
- human gate後の外部操作は承認された範囲だけで実行され、結果URLやIDを確認できる
- 最終レポートが変更内容、検証結果、残った制約をまとめている

構文と全オプションは[`cockpit fleet` Reference](https://agi-labo.com/tools/cockpit/docs/cockpit-cli/reference/fleet)、一件の委任と親子タスクは[タスク管理（CLI）](https://agi-labo.com/tools/cockpit/docs/task-management)、分解方法そのものを任せる場合は[Master Agent](https://agi-labo.com/tools/cockpit/docs/master-agent)を参照してください。
