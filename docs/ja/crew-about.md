<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# クルーとは

クルーでできること、できないこと、タスクやMaster Agentとの違い、使えるエージェント、料金とクルーの数の上限を説明します。

> AGI Cockpit 4.132.0で2026-11-13に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/crew-about)

クルーは、AGI Cockpitに常駐するAIエージェントです。役割と記憶を持ち、頼まれた仕事を自分で進めるか、子タスクに任せて、結果を返します。チャットにつながなくても、Cockpitの中で話せます。Discord・Slack・Chatwork（Chatworkは会員）につなぐと、そこでも話せます。

クルーはα版です。動きや設定は変わることがあります。

まだクルーがいなければ、[最初のクルーを作る](https://agi-labo.com/tools/cockpit/docs/crew-first)から始めてください。

**次に[承認モードと権限](https://agi-labo.com/tools/cockpit/docs/crew-approval)を読んでください。** 初めての人がいちばんつまずく所です。クルーの返事が止まる時は、承認を待っていることがよくあります。

## タスク・Master Agentとの違い

| | 向いている仕事 | 続き方 |
| --- | --- | --- |
| タスク | 一つの明確な成果を頼む | その仕事が終われば完了 |
| [Master Agent](https://agi-labo.com/tools/cockpit/docs/master-agent) | 目的から分解して、担当や優先順位を調整する | 目的を進める間 |
| クルー | 決まった役割を任せ続ける。会話やチャットで頼む | 役割と記憶を持ち、仕事をまたいで続く |

クルーはタスク一覧に並びます。クルーが作った子タスクは、その下に入ります。

## クルーにできること

- 依頼を受ける: Cockpitの会話か、Discord・Slack・Chatwork（Chatworkは会員）のDMやチャンネルで受けます
- 作業する: 自分で進めるか、子タスク（Claude Code・Codexなど）に任せて、結果を受け取るまで見届けます
- 見やすく返す: 比べる時などに、表・グラフ・カードをチャットに投稿します
- 覚える: 役割と決まりを`AGENTS.md`に、覚えたことと作業の記録をホームに残します。詳しくは[ホームと記憶](https://agi-labo.com/tools/cockpit/docs/crew-home)を見てください
- 自分から確かめる: 決まった間隔で自分の用件を見直します。詳しくは[定期確認と自動実行](https://agi-labo.com/tools/cockpit/docs/crew-schedule)を見てください
- 長い仕事を進める: 続く仕事を[ミッション](https://agi-labo.com/tools/cockpit/docs/crew-missions)として任せると、進み具合を「ミッション」タブで見られます
- 判断を聞く: 判断が要る時はAskで聞きます。AskはDiscord・Slack・ChatworkのDMでも受けて答えられます。詳しくは[Askとクルー](https://agi-labo.com/tools/cockpit/docs/crew-ask)を見てください

## 使えるエージェント

クルーを動かせるのは、Claude Code、Codex、Cursor、Qoder、Grok Build、Muse Code（macOSとLinuxだけ）、Cockpitです。

TerminalとAntigravityではクルーを動かせません。クルーが任せる子タスクには、Antigravityも使えます。エージェントごとの違いは[エージェントとモデル](https://agi-labo.com/tools/cockpit/docs/agents-and-models)を見てください。

## クルーにできないこと

| 思い込みやすいこと | 実際 |
| --- | --- |
| いつでも返事をする | クルーはオーナーのコンピューターの上で動きます。コンピューターが動いていて、Cockpitが動いている間だけ返事をします。スリープや電源オフの間は止まります。macOSとLinuxでは、ログインした時にCockpitを自動で起動できます |
| 止まっている間の発言も後で読む | クルーを止めている間と、Cockpitを閉じている間の発言は、後から取りに行きません。Chatworkだけは、Cockpitを閉じていた間の発言も後から届きます |
| 全部おまかせで動く | 承認モードによっては操作ごとに止まります。判断が要る時はAskで聞きます |
| どのチャットにもつながる | つながるのは、Discord・Slack・Chatwork（Chatworkは会員）の3つだけです。つなぎ方は[チャットにつなぐ](https://agi-labo.com/tools/cockpit/docs/crew-chat)を見てください |
| 話せる人が自分のアカウントで使う | ほかの人の依頼も、すべてオーナーのAIのアカウントで動きます |
| 決まりを書けば安全 | クルーの決まりは誤りを防ぐためのもので、安全の境界ではありません。詳しくは[安全と、人ごとの扱い](https://agi-labo.com/tools/cockpit/docs/crew-safety)を見てください |

AIサービスには、アカウントを他人に使わせることを禁じるものがあります。ほかの人を「話せる人」に加えるかどうかは、各サービスの規約を確かめて、オーナーが決めてください。

## 料金とクルーの数

- AGI Cockpitのローカル機能は無料（クルーは1体まで）です。アカウント登録も要りません
- 1体には、止めているクルーと、クルーが作ったクルーも数えます。前から作ってあるクルーは、何体でもそのまま使えます。上限で断られるのは、新しく作る時だけです
- AGIラボ会員は、クルーの数の上限がありません。会費は$20/月または$200/年で、7日間の無料トライアルがあります
- AIを動かす費用は、オーナーが使っているエージェントの契約やキー（Claude・ChatGPTなど）でまかないます。会費とは別です

クルーに関わる機能のうち、次もAGIラボ会員向けです。

- [自動実行](https://agi-labo.com/tools/cockpit/docs/autorun): 決まった時刻にクルーへ仕事を頼みます
- [リモートアクセス](https://agi-labo.com/tools/cockpit/docs/remote-access): スマートフォンやほかの端末からクルーを使います。ほかのコンピューターのクルーとのやり取りにも使います
- [Chatworkにつなぐ](https://agi-labo.com/tools/cockpit/docs/crew-chat): クルーをChatworkで話せるようにします

## どう頼むか

クルーへの頼み事は、同僚に話すように書いてかまいません。設定の変更も、まず話して頼めます。

- 「定期確認で見てほしいことを`HEARTBEAT.md`に足して」
- 「いま何を待っているか教えて」
- 「この調べものを子タスクに任せて、終わったら結果を要約して」

クルーが自分で変えられないものは、クルーがそう答えます。その時はオーナーがCockpitの画面で操作します。

## 関連ページ

- [最初のクルーを作る](https://agi-labo.com/tools/cockpit/docs/crew-first)
- [承認モードと権限](https://agi-labo.com/tools/cockpit/docs/crew-approval)
- [話せる人と場所](https://agi-labo.com/tools/cockpit/docs/crew-people)
- [クルーの働き方](https://agi-labo.com/tools/cockpit/docs/crew-work)
- [困った時](https://agi-labo.com/tools/cockpit/docs/crew-trouble)
