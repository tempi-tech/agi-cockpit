<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# AGI Cockpitとは

AGI CockpitがどこでAIエージェントを動かし、タスク一覧とタスク詳細を通じて人の判断と成果確認をつなぐのかを説明します。

> AGI Cockpit 4.114.0で2026-10-28に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs)

AGI Cockpitは、複数のAIエージェントへ仕事を渡し、進捗、判断要求、成果を一つの場所で監督するデスクトップアプリです。タスクを作るところから、結果を確認し、追加指示を送り、完了にするところまでを同じ作業単位で扱います。

プロジェクトは、一つ以上のフォルダを一つの名前でまとめる単位です。タスクはプロジェクトごとに整理され、各タスクは選択した一つの作業フォルダから開始します。対応するエージェントは、同じプロジェクトのほかのフォルダも利用できます。

## Cockpitが扱う仕事の流れ

1. プロジェクトを選択または作成し、作業フォルダと目的を設定する
2. 選択したAIエージェントを動かす
3. タスク一覧で、実行中、確認待ち、完了、エラーを見分ける
4. タスク詳細で会話、確認要求、差分、ファイル、Webページ、接続したアプリ画面などを確認する
5. 追加指示、Askへの回答、再開、完了のいずれかへ進む

Cockpitが仕事そのものをクラウドへ移すのではありません。エージェントのプロセスと作業ファイルはCockpitを実行しているコンピューター側にあり、利用するエージェントや外部サービスの通信条件はそれぞれの設定に従います。

アプリ内ブラウザーのログイン状態とサイトデータは、タスクまたはAutorunへ割り当てたBrowser Identityのローカル永続領域に保存されます。Identityごとに領域が分かれ、指定しないタスクとAutorunは従来のログイン状態を引き継ぐDefault Identityを使います。

## 4つの利用面

| 利用面 | 役割 | 主な利用者 |
| --- | --- | --- |
| Desktop | タスクの作成、一覧、詳細、設定、成果確認の中心 | 利用者 |
| エージェントプロセス | 指示を実行し、会話や変更をタスクへ返す | AIエージェント |
| PWA | 別端末からタスク、Ask、Autorunを確認・操作する | 利用者 |
| `cockpit` CLI | タスク、Ask、Autorun、ブラウザー、App SurfaceなどをCockpit経由で操作する | AIエージェントと利用者 |

ローカル機能はすべて無料・無制限で、アカウント登録も不要です。Claude CodeやCodexなどを使う場合は、各エージェントの契約が別途必要です。AGIラボ会員（$20/月または$200/年、7日間無料トライアル付き）になると、Autorun、PWAによるリモートアクセス、スマートルーティングの3つが加わります。

## クルー（アルファ版）

クルーは、継続的な役割・記憶・専用フォルダを持つエージェントです。依頼を自分で進めたり、子タスクへ委任したりします。アルファ版のため、挙動や設定は変わる場合があります。4.114.0はmacOSとLinuxで配布済みです。Microsoft Storeへの申請・公開は行っていません。

1. 作成画面で「タスク」ではなく「クルー」を選びます。プロジェクトまたは専用フォルダと、対応エージェントを選びます。TerminalとAntigravityではクルーを動かせません。
2. 役割を伝えるか、クルーからの質問に答えます。名前と`AGENTS.md`の指示案を確認し、合意してから保存させます。
3. Cockpit内から話しかけます。Discord接続は任意です。接続する場合はクルーの設定を開き、Discordアプリの作成、**Message Content Intent**の有効化、botトークンの入力、サーバーへの招待を進めます。最後にDMを送り、ユーザーIDを確認してオーナーとして承認します。
4. 依頼を受ける前に、許可する人と場所を確認します。未許可の人は承認待ちになり、承認前の発言は承認後も配送されません。チャンネルごとに、メンション時・全発言・オフを選んで受け付ける場所を決めます。

許可したほかの人からの依頼も、オーナーのAIアカウントで動きます。サービスの利用条件を確認し、発言の扱いを参加者へ知らせてください。慎重な承認モードではオーナーがCockpitで回答するまで返信が止まることがあります。「フルアクセス」では確認なしにコマンド実行・編集ができます。専用フォルダやクルー用CLIの制限はsandboxではありません。ほかの人を接続する前に[セキュリティとデータ](https://agi-labo.com/tools/cockpit/docs/security-and-data#クルーの権限と保存データ)を確認してください。

「定期確認」は既定でオンです。ホストの現地時刻08:00〜23:00に30分間隔で動きます。確認内容はホームの`HEARTBEAT.md`に記述し、設定では30〜240分を30分刻みで指定して時間帯も選べます。作業中・停止中・承認待ち・キュー待ち・再試行待ち・利用上限中や時間帯の外は飛ばします。クルーの「確かめることなし」の宣言が有効な間も省略できます。決まった指示を予定時刻に送る[Autorun](https://agi-labo.com/tools/cockpit/docs/autorun)とは別の機能です。

停止してもキューは残り、開始すると配送します。削除すると設定を削除し、専用フォルダのゴミ箱への移動を試みます。接続・記憶・再起動・削除の詳細は[クルーのCLIリファレンス](https://agi-labo.com/tools/cockpit/docs/cockpit-cli/reference/crew)を参照してください。

## 最初に読むページ

- アプリを準備する: [AGI Cockpitをインストールする](https://agi-labo.com/tools/cockpit/docs/getting-started)
- エージェントとCockpit連携を準備する: [初回セットアップ](https://agi-labo.com/tools/cockpit/docs/initial-setup)
- 最初の依頼を完了まで進める: [最初のタスク](https://agi-labo.com/tools/cockpit/docs/first-task)
- 複数の仕事を見分ける: [タスク一覧](https://agi-labo.com/tools/cockpit/docs/tasks)
- 選んだ仕事へ追加指示を送る: [タスク詳細](https://agi-labo.com/tools/cockpit/docs/task-details)
- 差分、ファイル、レポートを確認する: [成果とツール](https://agi-labo.com/tools/cockpit/docs/results-and-tools)
- 起動済みのモバイル画面を扱う: [App Surface](https://agi-labo.com/tools/cockpit/docs/app-surface)
- エージェントから人へ判断を渡す: [Ask](https://agi-labo.com/tools/cockpit/docs/ask)
- 一件のタスクをCLIから委任・監督する: [タスク管理（CLI）](https://agi-labo.com/tools/cockpit/docs/task-management)
- 大きな目的の分解と統合を任せる: [Master Agent](https://agi-labo.com/tools/cockpit/docs/master-agent)
- 依存関係付きの複数エージェント処理を再利用する: [Fleet](https://agi-labo.com/tools/cockpit/docs/fleet)
- 複数のエージェントと人が同じ会話で議論する: [Talk Rooms](https://agi-labo.com/tools/cockpit/docs/talk-rooms)
- 時刻や間隔に基づいて新しいタスクを起動する、または既存タスクへ指示を送る: [Autorun](https://agi-labo.com/tools/cockpit/docs/autorun)
- タスク完了やホットキーをきっかけに処理を実行する: [Hooks](https://agi-labo.com/tools/cockpit/docs/hooks)
- エージェントごとの対応差を調べる: [エージェントとモデル](https://agi-labo.com/tools/cockpit/docs/agents-and-models)
- 複数のエージェントアカウントを使い分ける: [アカウントとAuto](https://agi-labo.com/tools/cockpit/docs/accounts)
- 再利用する役割や品質基準を設定する: [システムプロンプト](https://agi-labo.com/tools/cockpit/docs/system-prompts)
- スマートフォンや別のコンピューターから監督する: [リモートアクセス](https://agi-labo.com/tools/cockpit/docs/remote-access)
- 3Dモデルまたは絵本を制作する: [Creative Studio](https://agi-labo.com/tools/cockpit/docs/creative-studio)
- 実際のWebページを人とエージェントで操作する: [cockpit browser](https://agi-labo.com/tools/cockpit/docs/browser)
- Webサイトのログイン状態を分離する: [Browser Identity](https://agi-labo.com/tools/cockpit/docs/browser-identities)
- AIエージェントやスクリプトからCockpitを操作する: [cockpit CLI](https://agi-labo.com/tools/cockpit/docs/cockpit-cli)

## 無料利用とAGIラボ会員

| 利用方法 | この初期ドキュメントで扱う範囲 |
| --- | --- |
| 無料利用 | Desktopでタスクを作成し、エージェントを実行して結果と履歴ダッシュボードを確認する。ローカル機能はすべて無料・無制限 |
| AGIラボ会員 | 無料利用の範囲に加え、Autorun、PWAによるリモートアクセス、スマートルーティングを利用する |

機能の入口に鍵が表示される場合、その機能には有効なAGIラボ会員資格が必要です。タスクの実行に使うClaude Code、Codexなどの認証は、AGIラボへのサインインとは別に管理されます。

## 次に進む

[AGI Cockpitをインストールする](https://agi-labo.com/tools/cockpit/docs/getting-started)、[初回セットアップ](https://agi-labo.com/tools/cockpit/docs/initial-setup)、[最初のタスク](https://agi-labo.com/tools/cockpit/docs/first-task)の順に進めます。
