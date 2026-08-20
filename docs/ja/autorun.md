<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Autorun

Autorunが一度、一定間隔、cronのスケジュールに基づいて新しいタスクを起動するか、既存タスクへ指示を送る仕組みを説明します。

> AGI Cockpit 4.51.0で2026-08-15に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/autorun)

Autorunは、指定した時刻、一定間隔、cron式に基づいて、新しいタスクを起動するか、既存タスクへ指示を送る仕組みです。複数エージェントを連携させる機能ではなく、同じ仕事を必要なタイミングで開始または継続するための独立した機能です。

## 利用条件

AutorunはAGIラボ会員向けです。会員状態はDesktopの入口だけでなく、PWA、`cockpit autorun`、ローカルAPI、スケジュール実行時にも確認されます。認証の更新が必要な状態、休止中の会員、一時的な確認障害は区別され、操作時には状態に合ったエラーが返ります。通信やAPIの一時障害で会員状態を確認できない場合も操作は安全側に拒否されますが、正規会員の復旧を長時間妨げないよう、その失敗結果はキャッシュされません。既存会員は、DesktopでAGIラボへサインインした状態のままCLIを利用できます。

スケジュールはAGI Cockpitのアプリプロセス内で動きます。macOSではウィンドウをすべて閉じてもスケジューラーは停止しません。プロセス終了後にOSのスケジューラーがCockpitを起動することはありませんが、未処理の予定時刻から24時間以内にCockpitが起動またはスリープ復帰すると、Autorunごとに1回だけ即時キャッチアップします。24時間を超えた回は古い作業を起動せず、未実行として記録します。

## 実行タイミング

| 種類 | 設定 | 動作 |
| --- | --- | --- |
| 一度のみ (`once`) | 日時 | 1回起動した後、自動的に無効になる |
| 間隔 (`interval`) | 分、時間、日 | 前回のスケジュール処理から指定間隔で起動する |
| Cron (`cron`) | `分 時 日 月 曜日` | cron式に一致する次の時刻に起動する |

cronの曜日は`0`が日曜日です。例として、平日の午前9時は`0 9 * * 1-5`です。

## Autorunを作成する

1. 画面左下のアプリメニューを開き、「自動実行タスク」を選びます。
2. 「新規作成」を選びます。
3. 名前と、起動時に送る指示を入力します。
4. 「実行先」で「新規タスクを作成」または「既存タスクへ送信」を選びます。
5. 新規タスクを作成する場合は、作業するディレクトリとエージェントを選び、表示されるUIモード、アカウント、モデル、推論レベル、service tier、system prompt、承認モードを確認します。Master Agentとして実行する場合はMasterの作業場所が使われ、アカウントを選べるエージェントの既定は「Auto」です。
6. 既存タスクへ送信する場合は「送信先タスク」を選びます。新しいランタイムは作られず、送信先のエージェント、会話、作業場所、ランタイム設定がそのまま使われます。
7. 一度のみ、間隔、Cronから実行タイミングを選びます。
8. 保存後、一覧に次回実行時刻が表示されることを確認します。

新規タスクを作成するAutorunは、保存時のランタイム設定をスナップショットとして保持します。後からグローバル設定を変更しても、既存Autorunのモデル、推論レベル、service tier、system prompt、承認モード、アカウントの選択方法、UIモードは自動で変わりません。固定したモデルやアカウントを利用できなくなった場合、Cockpitは別の設定へ無言で切り替えず、そのAutorunを無効にして「要確認」を表示します。

新規タスク用のBrowser Identity割り当てもAutorunごとに保存され、発火時に作成されるタスクへ引き継がれます。Identityを指定しないAutorunはDefault Identityを使います。v4.43.0では、Autorunへの割り当てと変更はCLIから行います。Identityそのものの作成、名前と色の変更、データ消去、削除は、画面左下のアプリメニューにある「Browser Identity」またはCLIから行えます。既存タスクへ送信するAutorunは、そのタスクにすでに割り当てられたBrowser Identityを変更しません。

新規タスクを作成する場合、DesktopとPWAでは選択したエージェントとUIモードで利用できる設定だけが表示されます。CLIとAPIも同じ対応情報を使い、非対応の組み合わせを保存前に明示的なエラーで拒否します。

CursorのAutorunは「ネイティブUI」と「ターミナル」を利用できます。ネイティブUIでは、Desktop、PWA、CLIからの作成時にCursorで利用可能なモデル、承認モード、アカウントプロファイルを選べます。Cursorでは、推論レベル、service tier、system promptは利用できません。

QoderのAutorunも「ネイティブUI」と「ターミナル」を利用できます。ネイティブUIでは、Desktop、PWA、CLIからの作成時にQoderで利用可能なモデル、system prompt、承認モード、アカウントプロファイルを選べます。Qoderでは、推論レベルとservice tierは利用できません。

Claude、Codex、Grok Build、Cursor、Qoderでは、「Auto」または固定アカウントをAutorunへ保存します。新しいAutorunの既定はAutoです。Autoは具体的なプロファイルではなく選択方法として保存され、発火するたびにログイン済みアカウントの利用状況から実行先を選び直します。作成されたタスクで利用上限に達した場合も、利用可能な別アカウントへ切り替えて処理を続けます。固定する場合、CLIでは`--account <name|id|default>`を使い、Autoへ戻す場合は`--account auto`を使います。

## 実行結果を確認する

新規タスクを対象にしたAutorunが発火すると、通常のタスクと同じ形で新しいタスクが作られます。タスク一覧から選び、タスク詳細で会話、確認要求、エラー、成果を確認します。作成されたタスクには元のAutorun IDが関連付けられます。

既存タスクを対象にしたAutorunは、そのタスクの次の指示として本文を送ります。送信先が入力可能ならすぐに送信し、実行中または利用上限で停止中なら、メインプロセスの永続キューへ保存して入力可能になるまで待ちます。キューにタイムアウトはなく、完了またはエラー状態のタスクは可能な場合に自動再開します。送信先の削除、再開不能、またはWindowsのTerminalタスクで前面プロセスを安全に確認できない場合は送信に失敗し、Autorunを無効にして「要確認」を表示します。

Autorun管理画面では、次を操作できます。

- 有効化と無効化
- 設定の編集
- 削除
- 「今すぐ実行」
- 前回実行時刻と次回実行時刻の確認
- 前回の実行状態と、作成先または送信先のタスクIDの確認

各予定時刻の結果は`created`、`queued`、`delivered`、`skipped-membership`、`skipped-lock`、`skipped-period`、`missed`、`failed`のいずれかで記録されます。`queued`は既存タスクへの指示が待機中、`delivered`は送信済みを表します。認証・会員状態によるスキップ、未実行、起動または送信の失敗はDesktopとPWAで「要確認」として表示され、デスクトップ通知も送られます。重複実行防止と同一期間の実行済みによるスキップは情報として表示されます。`cockpit autorun list`と`cockpit autorun get`でも同じ`lastRunStatus`、`lastRunScheduledAt`、`lastRunDetail`、`lastTaskId`を確認でき、`executionTarget`と`targetTaskId`で実行先も確認できます。`nextRunAt`は次の試行時刻、`nextRunScheduledAt`は再試行中も保持される本来の予定時刻です。また、AGI Toolsのデータディレクトリにある`data/cockpit/logs/autorun-executions.jsonl`へ、1回につき1行のJSONログを追記します。キューに入った回は送信完了または失敗時にも記録され、同じ結果の再試行は1回だけ記録します。ログは5 MBでローテーションして2世代の履歴を保持します。

新規タスクを作成するAutorunでは、起動処理が重なったときだけスケジュール実行を見送ります。ロックは起動処理のあいだだけ保持し、作成したタスクの完了は待ちません。前回作成したタスクが実行中でも、確認待ちでも、次の予定時刻には新しいタスクを作成します。起動処理が終わらないまま残ったロックは、最終的な保護として24時間で失効します。既存タスクへの送信も同じく発火後にロックを解放し、待機中の指示は送信先タスクごとに順番に処理します。

## 作業場所と失敗時の挙動

新規タスクを作成する場合、Desktopの作成画面ではMaster Agentでない限りディレクトリの指定が必要です。CLIからディレクトリを省略した場合は、OSの一時フォルダ内に`agi-cockpit`ディレクトリを作って実行します。既存タスクへ送信する場合は、そのタスクの作業場所を使うため、Autorun側でディレクトリを指定しません。

エージェントの起動に失敗しても、作成されたタスクは「エラー」としてタスク一覧に残ります。Autorun自体は、1回実行では無効になり、間隔またはcronでは有効なまま次回を計算します。原因は作成されたタスクの詳細から確認します。

予定時刻にゲスト状態または会員休止中と確認された場合、1回実行は起動せず無効になります。間隔またはcronは有効なまま、次の予定時刻に再確認します。一方、サインイン状態の更新が必要な場合や、通信・APIの一時障害で会員状態を確認できない場合は予定を実行せず、1分後に再確認します。この場合は1回実行も有効なまま再試行時刻が保存されるため、認証の更新や一時障害だけで実行機会を失いません。

## CLIから作成する

```bash
cockpit autorun create \
  --name "平日の進捗確認" \
  --instruction "このプロジェクトの未完了タスクを確認し、優先順に要約してください。" \
  --directory /path/to/project \
  --agent-type codex \
  --account auto \
  --ui-mode visual \
  --model gpt-5.4 \
  --effort high \
  --service-tier fast \
  --approval-mode accept-edits \
  --browser-identity work \
  --type cron \
  --expression "0 9 * * 1-5"
```

```bash
cockpit autorun list
cockpit autorun get <id>
cockpit autorun run <id>
cockpit autorun toggle <id>
cockpit autorun update <id> --browser-identity default
```

既存タスクへ送信する場合は、実行先とタスクIDを指定します。

```bash
cockpit autorun create \
  --name "進捗を継続" \
  --instruction "進捗を確認して、次の作業を続けてください。" \
  --execution-target existing-task \
  --target-task <task-id> \
  --type interval \
  --minutes 30
```

## 関連ページ

- [タスク一覧とタスク詳細](https://agi-labo.com/tools/cockpit/docs/tasks)
- [Ask](https://agi-labo.com/tools/cockpit/docs/ask)
