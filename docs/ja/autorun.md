<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Autorun

Autorunが一度、一定間隔、cronのスケジュールに基づいて新しいタスクを起動し、その結果を通常のタスクとして残す仕組みを説明します。

> AGI Cockpit 4.43.0で2026-08-02に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/autorun)

Autorunは、指定した時刻、一定間隔、cron式に基づいて、新しいタスクを自動起動する仕組みです。複数エージェントを連携させる機能ではなく、同じ条件の仕事を必要なタイミングで開始するための独立した機能です。

## 利用条件

AutorunはAGIラボ会員向けです。会員状態はDesktopの入口だけでなく、PWA、`cockpit autorun`、ローカルAPI、スケジュール実行時にも確認されます。認証の更新が必要な状態、休止中の会員、一時的な確認障害は区別され、操作時には状態に合ったエラーが返ります。通信やAPIの一時障害で会員状態を確認できない場合も操作は安全側に拒否されますが、正規会員の復旧を長時間妨げないよう、その失敗結果はキャッシュされません。既存会員は、DesktopでAGIラボへサインインした状態のままCLIを利用できます。

スケジュールはAGI Cockpitのアプリプロセス内で動きます。Cockpitが終了している間は発火せず、OSのスケジューラーがCockpitを起動することもありません。

## 実行タイミング

| 種類 | 設定 | 動作 |
| --- | --- | --- |
| 一度のみ (`once`) | 日時 | 1回起動した後、自動的に無効になる |
| 間隔 (`interval`) | 分、時間、日 | 前回のスケジュール処理から指定間隔で起動する |
| Cron (`cron`) | `分 時 日 月 曜日` | cron式に一致する次の時刻に起動する |

cronの曜日は`0`が日曜日です。例として、平日の午前9時は`0 9 * * 1-5`です。

## 自動実行タスクを作成する

1. 画面左下のアプリメニューを開き、「自動実行タスク」を選びます。
2. 「新規作成」を選びます。
3. 名前と、起動時に送る指示を入力します。
4. 作業するディレクトリを選びます。Master Agentとして実行する場合は、Masterの作業場所が使われます。
5. エージェントを選び、表示される場合はUIモード、アカウント、モデル、推論レベル、service tier、system prompt、承認モードを確認します。
6. 一度のみ、間隔、Cronから実行タイミングを選びます。
7. 保存後、一覧に次回実行時刻が表示されることを確認します。

Autorunは保存時のランタイム設定をスナップショットとして保持します。後からグローバル設定を変更しても、既存Autorunのモデル、推論レベル、service tier、system prompt、承認モード、アカウント、UIモードは自動で変わりません。保存済みのモデルやアカウントを利用できなくなった場合、Cockpitは別の設定へ無言で切り替えず、そのAutorunを無効にして「要確認」を表示します。

Browser Identityの割り当てもAutorunごとに保存され、発火時に作成されるタスクへ引き継がれます。Identityを指定しないAutorunはDefault Identityを使います。v4.43.0では、Autorunへの割り当てと変更はCLIから行います。Identityそのものの作成、名前と色の変更、データ消去、削除は、画面左下のアプリメニューにある「Browser Identity」またはCLIから行えます。

DesktopとPWAでは、選択したエージェントとUIモードで利用できる設定だけが表示されます。CLIとAPIも同じ対応情報を使い、非対応の組み合わせを保存前に明示的なエラーで拒否します。

CursorのAutorunは「ネイティブUI」と「ターミナル」を利用できます。ネイティブUIでは、Desktop、PWA、CLIからの作成時にCursorで利用可能なモデルと承認モードを選べます。Cursorでは、推論レベル、service tier、system prompt、アカウント設定は利用できません。

## 実行結果を確認する

Autorunが発火すると、通常のタスクと同じ形で新しいタスクが作られます。タスク一覧から選び、タスク詳細で会話、確認要求、エラー、成果を確認します。作成されたタスクには元のAutorun IDが関連付けられます。

Autorun管理画面では、次を操作できます。

- 有効化と無効化
- 設定の編集
- 削除
- 「今すぐ実行」
- 前回実行時刻と次回実行時刻の確認

同じAutorunの前回の実行ターンが処理中である間は、次のスケジュール実行を重ねません。Visual Runtime（画面上のネイティブUI）ではターン完了、runtime固有のsettle、または実行エラーの時点で、ターミナルUIとTerminalエージェントでは起動したプロセスの終了またはクリーンアップ時にロックを解除します。Cockpit上で人がタスクを「完了」にするまで保持するロックではありません。起動失敗や後処理でも解除され、異常終了への最終的な保護として24時間で失効します。

## 作業場所と失敗時の挙動

Desktopの作成画面では、Master Agentでない限りディレクトリの指定が必要です。CLIからディレクトリを省略した場合は、OSの一時フォルダ内に`agi-cockpit`ディレクトリを作って実行します。

エージェントの起動に失敗しても、作成されたタスクは「エラー」としてタスク一覧に残ります。Autorun自体は、1回実行では無効になり、間隔またはcronでは有効なまま次回を計算します。原因は作成されたタスクの詳細から確認します。

予定時刻にゲスト状態または会員休止中と確認された場合、1回実行は起動せず無効になります。間隔またはcronは有効なまま、次の予定時刻に再確認します。一方、サインイン状態の更新が必要な場合や、通信・APIの一時障害で会員状態を確認できない場合は予定を実行せず、1分後に再確認します。この場合は1回実行も有効なまま再試行時刻が保存されるため、認証の更新や一時障害だけで実行機会を失いません。

## CLIから作成する

```bash
cockpit autorun create \
  --name "平日の進捗確認" \
  --instruction "このプロジェクトの未完了タスクを確認し、優先順に要約してください。" \
  --directory /path/to/project \
  --agent-type codex \
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

## 関連ページ

- [タスク一覧とタスク詳細](https://agi-labo.com/tools/cockpit/docs/tasks)
- [Ask](https://agi-labo.com/tools/cockpit/docs/ask)
