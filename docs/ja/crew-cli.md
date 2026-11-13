<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# CLIの入口

クルーに頼んだ時に裏で使われるcockpit crewコマンドを、作る・設定・つなぐ・話せる人と場所・チャットの操作・ミッション・ほかのコンピューターの用途ごとにまとめた地図です。

> AGI Cockpit 4.132.0で2026-11-13に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/crew-cli)

クルーを使うのに、コマンドを打つ必要はありません。画面で操作するか、クルーやマスターエージェントに頼めば、エージェントが裏で`cockpit crew`のコマンドを使います。

このページは、その裏で使われるコマンドの地図です。クルーの報告や作業の記録に出てきたコマンドが何をするのかを知りたい時に使います。オプションや結果の形は、最後の[詳しく知る](#詳しく知る)から読めます。

`<crew>`には、クルーのIDか名前を入れます。

## 作る・見る

| コマンド | すること | 頼み方の例 |
| --- | --- | --- |
| `cockpit crew list` | クルーの一覧 | 「クルーの一覧を見せて」 |
| `cockpit crew get <crew>` | 設定と動いている様子。資格情報は返しません | 「経理のクルーの設定を教えて」 |
| `cockpit crew status [<crew>]` | 待っていること、子タスク、Ask、キューを1画面にまとめる | 「いま何を待っているか教えて」 |
| `cockpit crew create --name <name>` | 新しいクルーを作る | 「経理のクルーを作って」 |
| `cockpit crew create --from-task <task-id>` | 今のタスクを、会話を保ったままクルーにする | タスクのメニューの「クルーにする」と同じ |
| `cockpit crew remove <crew>` | クルーを削除する。ホームはゴミ箱へ移る | オーナーだけ |

## 設定と動かし方

| コマンド | すること |
| --- | --- |
| `cockpit crew update <crew> ...` | 名前、エージェント、モデル、承認モード、アカウント、定期確認、稼働時間、動く場所、アイコン、プロフィールの説明、チャットの書き方、作業中の割り込みなどを変える |
| `cockpit crew instructions <crew> --get` | `AGENTS.md`を読む |
| `cockpit crew instructions <crew> --set-stdin` | `AGENTS.md`を書き換えて、commitする（4,000字まで） |
| `cockpit crew start <crew>`・`stop <crew>` | 起動・停止 |
| `cockpit crew stop <crew> --turn` | 今のターンだけを止める |
| `cockpit crew restart [<crew>]` | 新しいセッションで始め直す |
| `cockpit crew check [<crew>]` | 定期確認で見るものを、まとめて読む |
| `cockpit crew open-home <crew>` | ホームのフォルダを開く |
| `cockpit crew send <crew> --text "..."` | Cockpitの中で、クルーに文を送る |

決まった時刻にクルーへ仕事を頼む時は、`cockpit autorun`を使います。[自動実行](https://agi-labo.com/tools/cockpit/docs/autorun)を見てください。

## チャットにつなぐ

| コマンド | すること |
| --- | --- |
| `cockpit crew setup begin <crew> --platform discord` | つなぐ手順を始める。Slackは`--platform slack`、Chatworkは`--platform chatwork` |
| `cockpit crew connect <crew> --platform discord --secure-input` | オーナーのCockpitにトークンの入力欄を出す。トークンはエージェントに届かない。ChatworkのAPIトークンは`--platform chatwork` |
| `cockpit crew setup slack <crew>` | Slackのアプリを作る手順を進める |
| `cockpit crew invite-url <crew>` | Discordのサーバーへの招待リンクを出す |
| `cockpit crew setup chatwork <crew> --start` | Chatworkの受け取りをAGIラボに用意する（AGIラボ会員） |
| `cockpit crew setup chatwork <crew> --webhook-url` | ChatworkのWebhookに貼る受け取りの住所を出す |
| `cockpit crew setup chatwork <crew> --webhook-key-stdin` | Webhookのトークンを渡す |
| `cockpit crew setup chatwork <crew>` | Chatworkの設定がどこまで進んだかを見る |
| `cockpit crew setup owner <crew> --yes <user-id>` | DM（Chatworkではコンタクトの申請）を送った人をオーナーにする |
| `cockpit crew disconnect <crew>` | 接続を外し、資格情報を消す |

`setup chatwork`を使えるのはオーナーの側だけです。クルー自身と、その子タスクからは使えません。

## 話せる人と場所

| コマンド | すること |
| --- | --- |
| `cockpit crew people add`・`remove` | 話せる人を加える・外す |
| `cockpit crew people owner` | オーナーにする・オーナーから外す |
| `cockpit crew pending list`・`approve`・`deny` | 承認待ちの人を見る・許可する・断る |
| `cockpit crew place set`・`remove` | チャンネルでの反応を決める・一覧から外す |
| `cockpit crew place dm`・`other`・`threads` | DM、一覧にないチャンネル、スレッドでの反応を決める |
| `cockpit crew place people add`・`remove` | そのチャンネルだけで話せる人を決める |
| `cockpit crew channels`・`groups` | クルーが見られるチャンネル、ロールやユーザーグループの一覧 |

## チャットの操作

クルー自身が、チャットで返事をする時に使います。

| コマンド | すること |
| --- | --- |
| `cockpit crew reply` | 発言に返事をする |
| `cockpit crew post` | 場所を選んで投稿する。表・グラフ・カード・ボタンも付けられる |
| `cockpit crew edit`・`delete` | クルーの投稿を直す・消す |
| `cockpit crew react` | リアクションを付ける・外す。Chatworkでは使えない |
| `cockpit crew thread` | スレッドを作る・名前を変える。Chatworkでは使えない |
| `cockpit crew history` | 場所の最近の発言や、リンク先の発言を読む |
| `cockpit crew threads` | 読めるスレッドの一覧。Chatworkでは使えない |

## ミッション

| コマンド | すること |
| --- | --- |
| `cockpit crew mission list`・`get` | ミッションの一覧と中身を読む |
| `cockpit crew mission create` | ミッションを作る |
| `cockpit crew mission update` | 今の状況、次の一手、待っていることを書く。止める・再開する |
| `cockpit crew mission log` | 記録を足す |
| `cockpit crew mission end` | 達成または中止で終える |

## ほかのコンピューター

`--host <host>`を付けると、同じTailscaleの持ち主の別のコンピューターのクルーを扱えます。使えるのは`list`、`get`、`status`、`invite-url`、`channels`、`groups`、`send`だけです。相手のCockpitのリモートアクセスがオンである必要があります。

| コマンド | すること |
| --- | --- |
| `cockpit crew list --host <host>` | 相手のコンピューターのクルーの一覧 |
| `cockpit crew status <crew> --host <host>` | 相手のクルーの様子 |
| `cockpit crew send <crew> --host <host> --text "..."` | 相手のクルーに文を送る。添付は送れない |

## 誰が使えるか

同じコマンドでも、誰が使うかで、できることが変わります。

- **オーナーの側**（オーナーのタスク、マスターエージェント、Cockpitの外のシェル）は、すべて使えます
- **クルー自身**ができることは、今のターンを誰が始めたかで変わります。断られると`owner_request_required`や`not_allowed_for_crew`が返ります
- **クルーの子タスク**は、自分のクルーを読むことと、文を送ることが中心です。設定は変えられません

詳しくは[安全と、人ごとの扱い](https://agi-labo.com/tools/cockpit/docs/crew-safety)と[複数のクルー](https://agi-labo.com/tools/cockpit/docs/crew-multiple)を見てください。エラーの意味は[困った時](https://agi-labo.com/tools/cockpit/docs/crew-trouble)にあります。

## 詳しく知る

- `cockpit help crew`: クルーのコマンドの正本です。作業の進め方は`cockpit help crew-work`、設定の変え方は`cockpit help crew-settings`、ミッションは`cockpit help crew-missions`にあります
- [クルーのCLIリファレンス](https://agi-labo.com/tools/cockpit/docs/cockpit-cli/reference/crew): `cockpit help crew`と同じ中身をWebで読めます
- [cockpit CLI](https://agi-labo.com/tools/cockpit/docs/cockpit-cli): CLI全体の考え方と、[ほかのコンピューターのクルー](https://agi-labo.com/tools/cockpit/docs/cockpit-cli#ほかのコンピューターのクルーを確認して依頼する)の説明があります

## 関連ページ

- [クルーとは](https://agi-labo.com/tools/cockpit/docs/crew-about)
- [安全と、人ごとの扱い](https://agi-labo.com/tools/cockpit/docs/crew-safety)
- [困った時](https://agi-labo.com/tools/cockpit/docs/crew-trouble)
- [cockpit CLI](https://agi-labo.com/tools/cockpit/docs/cockpit-cli)
