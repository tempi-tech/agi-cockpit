<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# タスク管理（CLI）

cockpit taskでタスクを作成・委任し、状態とレポートを確認して、追加指示、再開、完了まで安全に管理する方法を説明します。

> AGI Cockpit 4.71.0で2026-09-05に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/task-management)

`cockpit task`は、AIエージェントや利用者がCockpitのタスクを作成し、状態を読み、次の指示を送り、結果を回収するためのCLIです。一件の仕事を別タスクへ委任する場合は、このページの流れを使います。依存関係付きの処理をYAMLで再利用する場合は[Fleet](https://agi-labo.com/tools/cockpit/docs/fleet)を選びます。

## createとrunを使い分ける

| 目的 | コマンド | 戻り方 |
| --- | --- | --- |
| タスクを作成してIDを受け取る | `cockpit task create` | 作成結果をすぐ返す |
| タスクを作成し、最初のレポートまで待つ | `cockpit task run` | 最初の停止点、エラー、完了で返す |
| シェルコマンドだけを実行する | `cockpit task create --agent-type terminal --command "..."` | Terminalタスクを作成する |

別タスクの結果が次の判断に必要なら`run`を使います。作成後すぐ別の作業へ進み、あとで一覧から確認するなら`create`を使います。

```bash
cockpit task run \
  --instruction "変更を調査し、原因と確認結果を報告してください" \
  --directory /path/to/repo \
  --agent-type codex
```

長い指示、Markdown、引用、バッククォート、`$`を含む指示はシェル引数へ埋め込まず、`--stdin`または`--instruction-file`で渡します。

```bash
cockpit task run --instruction-file instruction.md \
  --directory /path/to/repo \
  --agent-type codex
```

`create`が成功すると、作成した`taskId`と実際に選ばれたアカウントが返ります。`run`も、タスク作成後に最初のレポート取得がタイムアウトまたは待機エラーになった場合は、`ok: true`と`data.taskId`を保持して返します。この場合、タスクはすでに存在するため同じ指示を再実行せず、返されたIDで`task get`または`task wait`を使ってください。`ok: false`はタスク作成自体の失敗を示します。

## 作成応答が失われた場合

`task create`と`task run`は作成ごとに一意の作成キーを送り、送信後に接続が切れた場合は、同じキーですでに作成されたタスクを先に照合します。見つかったタスクは`data.replayed: true`と同じ`taskId`で返るため、応答だけが失われても重複作成しません。未作成とCockpitが確認できた場合だけ同じキーで作成を再試行します。

`task_create_interrupted`はタスクが作成されていないことを確認済みで、同じコマンドを再実行できます。`task_create_outcome_unknown`は結果を確認できず、タスクが存在する可能性を示します。この場合は新しく作成する前に`cockpit task list`を確認してください。作成キーの記録は実行中のCockpitが10分間保持するため、アプリの再起動後は同じキーでも既存タスクを照合できません。

通常はCLIがUUIDを生成します。外部処理と対応づける場合だけ、英数字と`.`、`_`、`:`、`-`からなる1〜128文字を`--create-key`へ指定できます。同じ呼び出し元から同じキーを再送すると同じタスクを返します。

```bash
cockpit task create \
  --instruction "公開状態を確認してください" \
  --directory /path/to/repo \
  --create-key deploy-2026-09-05-1
```

## 作業場所と実行設定を決める

既存プロジェクトを扱う場合は`--directory`を指定します。省略するとOSの一時フォルダーで始まり、完了時に作業場所が削除されることがあります。Git Worktreeを分離する場合は`--worktree`を使います。

エージェント、モデル、推論レベル、アカウント、承認モード、Browser Identityはタスクごとの実行条件です。対応しない組み合わせはエラーになり、無言で別設定へ切り替わりません。特に外部サイトを扱うタスクでは、必要なログイン状態を持つBrowser Identityを明示します。

```bash
cockpit task create \
  --instruction "管理画面の公開状態を確認してください" \
  --directory /path/to/repo \
  --agent-type codex \
  --browser-identity work
```

## 親子タスクを作る

タスク内から作成したタスクは、既定で呼び出し元の子タスクになります。別の親を明示する場合は`--parent-task-id`を使います。親子関係はタスク一覧と子タスクパネルの階層を作りますが、結果配送の契約ではありません。

子タスクが停止点へ達しても、親が自動的に全成果を受け取るとは限りません。親側は`task run`の返り値、`task wait`、または`task get`から必要なレポートを読み、差分、テスト、URLなど依頼した証拠を確認します。

## 状態と待機理由を読む

```bash
cockpit task list
cockpit task list --status waiting_confirmation
cockpit task get <task-id> --turns 3 --max-lines 500
```

| フィールド | 判断 |
| --- | --- |
| `running` | 実行中。待つか、必要な場合だけ追加指示を検討する |
| `waiting_confirmation` | 入力待ち。`waitingReason`と`readyForNextPrompt`を確認する |
| `completed` | プロセスが終了した状態。成果確認が終わった意味ではない |
| `error` | 起動または実行に失敗。`errorMessage`と直近の会話を読む |
| `needsResume: true` | プロセスが止まっているため、同じタスクを再開できる |

`waitingReason`が`permission`または`question`なら、エージェント内の確認が続いています。`readyForNextPrompt`がfalseの間に別の指示を重ねません。`usage_limit`なら利用できるアカウントとリセット時刻を確認します。

## レポートを順番に受け取る

`task run`と`task wait`が返す`report.seq`はタスクごとに増加します。処理済みの番号を`--since`へ渡すと、同じレポートを重複処理せず次のレポートを待てます。

```bash
cockpit task wait <task-id> --since <last-seq> --timeout 110
```

`timeout: true`はタスク失敗ではありません。現在状態を必要に応じて確認し、同じ`--since`から待機を続けます。短い間隔で`task get`を繰り返す代わりに、継続監視には`task wait`を使います。

## 追加指示を送る

タスクが次の指示を受け取れる状態なら`task send`を使います。そのターンのレポートまで必要な場合は`--wait`を付けます。

```bash
cockpit task send <task-id> --text "失敗したテストだけ修正し、再実行してください" --wait
```

複数行の指示は`--stdin`または`--text-file`で渡します。確認画面に対してEnterを送るだけなら、本文を付けず`cockpit task send <task-id>`を実行します。送信前に`waitingReason`を読み、質問への回答なのか、ツール許可なのか、通常の追加指示なのかを区別してください。

## アカウントとBrowser Identityを切り替える

利用上限などで続行できない場合、対応するエージェントではタスクのアカウントを切り替えられます。切り替え後、停止理由に応じて続行指示を送ります。

```bash
cockpit task account <task-id> work
```

Browser Identityの変更は、そのタスクの次のブラウザーセッションへ使う永続領域を切り替えます。CookieやlocalStorageをIdentity間でコピーしません。

```bash
cockpit task browser-identity <task-id> work
```

## 完了、再開、削除を区別する

| 操作 | 結果 |
| --- | --- |
| `task resume` | 停止したタスクを同じ履歴で再開する |
| `task complete` | プロセスを止めて完了へ移す。CLIではWorktreeを既定で削除する |
| `task complete --keep-worktree` | Worktreeを残して完了へ移す |
| `task remove` | タスクと履歴をCockpitから削除する |

完了や削除は、利用者が明示的に求めた場合だけ行います。差分、生成物、レポート、公開URLなど必要な成果を保存し、対象IDを再確認してから実行してください。

## 完了を判定する

タスク管理の完了は、表示上の状態だけでなく次を確認します。

- 依頼した成果と証拠をレポートまたは実ファイルから回収した
- 確認待ち、利用上限、エラー、タイムアウトを未解決のまま残していない
- 親タスクが子タスクの結果を必要な順序で統合した
- 外部公開や削除は承認された対象だけに行われた
- 作業場所を消す前に必要な変更を保存した

全構文は[`cockpit task` Reference](https://agi-labo.com/tools/cockpit/docs/cockpit-cli/reference/task)、画面上の確認は[タスク一覧](https://agi-labo.com/tools/cockpit/docs/tasks)と[タスク詳細](https://agi-labo.com/tools/cockpit/docs/task-details)を参照してください。
