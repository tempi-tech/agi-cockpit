<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# リモートアクセス

TailscaleとHTTPSを使って、スマートフォンや別のコンピューターからAGI Cockpitのタスク、Ask、Autorunを安全に監督する手順です。

> AGI Cockpit 4.61.0で2026-08-27に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/remote-access)

リモートアクセスを有効にすると、AGI Cockpitを実行しているコンピューターへ、スマートフォン、タブレット、別のコンピューターのブラウザーから接続できます。この手順では、推奨構成のTailscaleとHTTPSを使い、PWAでタスクを確認できるところまで進めます。

## 仕組み

Cockpitを実行しているコンピューターが接続先になり、ブラウザーで開いたPWAがそのCockpitを操作します。タスク、エージェントのプロセス、作業ファイルは接続先のコンピューターに残り、PWAへ移動しません。

| モード | 接続できる範囲 | 通信 |
| --- | --- | --- |
| オフ | このコンピューターのDesktopだけ | リモート接続なし |
| Tailscale限定 | 同じtailnetから | Tailscaleの暗号化されたネットワーク。HTTPSを追加可能 |
| ローカルWi-Fiも許可 | 同じLANから | HTTP。暗号化なし |

リモートアクセス中は、接続先のAGI Cockpitプロセスとリモートアクセスを動かしておく必要があります。複数のCockpitがある場合も、タスクと設定は接続先ごとに分かれたままです。

## 始める前に

| 必要なもの | 確認すること |
| --- | --- |
| AGIラボ会員 | Desktopで有効な会員アカウントへサインインしている |
| Tailscale | 接続先と接続元の両方にインストールし、同じtailnetへ接続している |
| Tailscaleの管理権限 | HTTPSを初めて有効にする場合、Owner、Admin、またはNetwork adminが設定する |
| ブラウザー | 接続元で最新のSafari、Chrome、Edgeなどを利用する |

Tailscaleをまだ利用していない場合は、[Tailscaleをインストール](https://tailscale.com/download)し、接続する両方の端末で同じtailnetへサインインします。

## TailscaleとHTTPSで接続する

### 1. Tailscale限定でリモートアクセスを開始する

1. Desktop左下の「リモートアクセス」を開きます。
2. Tailscaleが停止中または未認証と表示された場合は、画面の案内から起動またはログインします。
3. 「Tailscale限定」を選びます。
4. 「接続情報」にURLとQRコードが表示されることを確認します。

Tailscale限定は、Tailscaleに属さない接続を受け付けません。HTTPSがまだオフでもTailscaleの端末間通信は暗号化されますが、ブラウザーにはHTTPの接続元として扱われます。ブラウザーの警告を避け、HTTPSを前提とする機能を利用できるよう、次の手順で証明書を設定してください。

### 2. HTTPS証明書を取得する

1. リモートアクセス画面の「証明書を取得」を選びます。
2. `your Tailscale account does not support getting TLS certs`と表示された場合は、[TailscaleのDNS設定](https://login.tailscale.com/admin/dns)を開きます。
3. Tailscale管理画面でMagicDNSを有効にします。
4. HTTPS Certificatesで「Enable HTTPS」を選び、表示される説明を確認して有効にします。
5. Cockpitへ戻り、証明書をもう一度取得します。
6. 「HTTPS証明書発行済み」とドメインが表示されることを確認します。

[TailscaleのDNS設定](https://login.tailscale.com/admin/dns)はブラウザーから直接開けます。設定を変更できない場合は、対象tailnetのOwner、Admin、またはNetwork adminへ依頼してください。

TailscaleのHTTPSを有効にすると、証明書に含まれる端末名とtailnetのDNS名が公開のCertificate Transparencyログへ記録されます。機密情報を含む端末名は、証明書を取得する前に変更してください。詳しくは[TailscaleのHTTPS設定](https://tailscale.com/docs/how-to/set-up-https-certificates)を参照してください。

### 3. HTTPSを有効にしてPWAを開く

1. 「HTTPS接続」をオンにします。
2. 接続情報が`https://端末名.tailnet名.ts.net:47280`形式になったことを確認します。
3. 接続元でQRコードを読み取るか、表示されたURLを開きます。
4. デバイス認証が表示された場合は、Desktopのリモートアクセス画面にある6桁のペアリングコードを入力します。

同じ利用者とtailnetに属するTailscale端末は、Tailscaleの端末情報で自動的に認証される場合があります。自動認証できない接続ではペアリングコードが必要です。コードは5分で更新され、「再生成」を選ぶとその場で新しくできます。

接続に成功すると、PWA上部に接続先と「接続済み」が表示され、「確認」「タスク」「自動タスク」を開けます。

## PWAで監督する

| 画面 | 主な操作 |
| --- | --- |
| 確認 | 保留中のAskを読み、選択、自由入力、添付を含む回答を返す |
| タスク | タスクの作成、一覧と詳細の確認、追加指示または次のメッセージのキュー送信、権限や質問への回答、完了、削除を行う |
| 自動タスク | Autorunの作成、編集、有効化、今すぐ実行、削除、結果確認を行う |

PWAは、外出先や別の部屋から進行状況と判断待ちを確認するための操作面です。リモートアクセス自体の有効化、通信モード、HTTPS証明書はDesktopで設定します。Desktopにあるすべての設定画面や結果表示がPWAにもあるわけではありません。

実行中のタスクから送信したメッセージは現在のターンを止めずにキューへ入り、返答後に自動送信されます。Claude、Codex、Grok BuildのネイティブUIでは、`/compact`だけを送ると会話コンテキストを圧縮します。圧縮中の通常メッセージもキューに保存され、対応しないネイティブUIの`/compact`は通常メッセージとして送られず、利用できないことが表示されます。

ホーム画面に追加する前に、Desktopで**HTTPS**をオンにし、接続情報の`https://...` URLを開いてください。HTTPのURLは、AGI CockpitをPWAとして利用する構成の対象外です。HTTPSで開いた後、iPhoneまたはiPadのSafariでは共有メニューから「ホーム画面に追加」、AndroidのChromeではブラウザーメニューから「アプリをインストール」または「ホーム画面に追加」を選びます。

## 複数のCockpitを切り替える

複数のコンピューターでリモートアクセスを有効にしている場合、PWA上部の接続先を選ぶと、Tailscale上で検出できたほかのCockpitを確認できます。「開く」で別の接続先へ移動し、「再検出」で一覧を更新します。

各Cockpitは独立した接続先です。切り替えても、タスク、Autorun、設定、作業ファイルがコンピューター間で統合または移動することはありません。

## ローカルWi-Fiで接続する

Tailscaleを使えず、信頼できる同一LAN内だけで一時的に接続する場合は、「ローカルWi-Fiも許可」を選べます。確認画面でリスクを了承した後、`http://ローカルIP:47280`形式のURLを開き、6桁のペアリングコードを入力します。

ローカルWi-Fi接続はHTTPSを利用できません。同じWi-Fi上の第三者がtokenやタスク内容を読み取れる可能性があります。公共Wi-Fi、共有オフィス、来訪者が接続できるネットワークでは使用しないでください。ルーターのポート転送でインターネットへ公開する用途にも使わないでください。

## 画面オフでも接続を保つ

ノートPCの画面を消灯すると、本体を起こしておく要因がなくなり、しばらく操作がないまま本体がスリープして、リモートアクセスへ到達できなくなります。Tailscale上でも`offline`と表示されます。

Desktopのリモートアクセス画面で「このコンピューターをスリープさせない」をオンにすると、リモートアクセス実行中は、操作がなくても本体がスリープしなくなります。ディスプレイのスリープは妨げないため、画面は通常どおり消灯します。既定はオフで、オンにした設定はすぐに保存されます。サーバーの再起動は不要です。

抑止できるのは、操作がないことによるスリープだけです。ふたを閉じる、Appleメニューから「スリープ」を選ぶなど、明示的なスリープは抑止できず、リモートアクセスも切断されます。この設定に頼る間は、ふたを開けたままにしてください。

抑止が働くのはリモートアクセス実行中だけです。リモートアクセスをオフにする、このトグルをオフにする、AGI Cockpitを終了する、のいずれでも解除されます。バッテリー駆動中は本体が起きたままになり、電池が減り続けます。外出先では電源に接続するか、使い終わったらリモートアクセスをオフにしてください。

## 接続を停止する

Desktopのリモートアクセス画面で「オフ」を選ぶと、接続中のセッションが切断され、接続先はローカル利用だけに戻ります。再び有効にした後、自動認証されない端末は新しいペアリングコードで認証します。

AGIラボのサインインまたは会員資格が無効になった場合も、Cockpitは実行中のリモートアクセスを停止し、オフの状態へ戻します。一時的な会員確認障害では、実行中の接続を直ちに停止せず、画面に再確認の案内を表示します。

## CLIから確認・設定する

AIエージェントまたはターミナルから、現在の状態と前提条件を確認できます。

```bash
cockpit remote-access status --verbose
cockpit remote-access tailscale status
cockpit remote-access certificate status
```

停止中のCockpitをTailscaleとHTTPSで構成する場合は、証明書を取得してから設定を保存し、有効にします。

```bash
cockpit remote-access certificate generate
cockpit remote-access configure --scope tailscale --https true --keep-awake true
cockpit remote-access enable
```

`--keep-awake true|false`はスリープ抑止トグルに対応します。`cockpit remote-access status`では、保存された設定が`configured.keepAwake`、実際に抑止中かどうかが`runtime.keepingAwake`に表示されます。

実行中は構成を変更できません。変更が必要な場合は、接続中のセッションが終了することを確認してから`cockpit remote-access disable --confirm`を実行します。ローカルネットワークをCLIから有効にする操作には、構成時と開始時の両方で`--confirm-local-network`が必要です。

## 接続できない場合

| 表示または状態 | 確認すること |
| --- | --- |
| リモートアクセスに鍵が表示される | 有効なAGIラボ会員アカウントでDesktopへサインインする |
| Tailscaleが未導入、停止中、未認証 | 画面の案内からインストール、起動、ログインを行う。接続元も同じtailnetへ接続する |
| `your Tailscale account does not support getting TLS certs` | [TailscaleのDNS設定](https://login.tailscale.com/admin/dns)でMagicDNSとHTTPS Certificatesを有効にし、Cockpitで証明書をもう一度取得する |
| Tailscaleの設定を変更できない | Owner、Admin、またはNetwork adminへ依頼する |
| 証明書は発行済みだがURLがHTTP | 「HTTPS接続」をオンにし、接続情報がHTTPSへ変わることを確認する |
| URLまたはQRコードが表示されない | 「Tailscale限定」または「ローカルWi-Fiも許可」が選ばれ、接続先の状態が動作中になっているか確認する |
| ペアリングコードを受け付けない | 5分以内の最新コードを使う。「再生成」後は新しいコードを入力する。3回失敗した場合は30秒待つ |
| PWAが「切断」と表示する | 接続先のCockpit、リモートアクセス、Tailscaleが動作中か確認し、「今すぐ再試行」を選ぶ |
| ほかのCockpitが見つからない | 各コンピューターでリモートアクセスを有効にし、同じtailnetへ接続してから「再検出」を選ぶ |
| 会員資格を確認できない | 通信状態を確認し、「会員資格をもう一度確認」を選ぶ |

Tailscale本体は動作していても、CockpitがTailscale CLIの状態を取得できない場合があります。画面にCLI確認エラーが表示され続ける場合は、Tailscaleを再インストールしてからもう一度確認してください。

## セキュリティ上の注意

- 通常は「Tailscale限定」とHTTPSを利用します。
- Tailscaleのアクセス制御では、Cockpitへ接続できる端末と利用者だけを許可します。
- PWAからはタスクの指示、Askへの回答、Autorunの変更などを実行できます。接続済み端末をロックし、共有端末には認証情報を残さないでください。
- HTTPS証明書を取得する前に、公開ログへ記録されてもよい端末名か確認します。
- ローカルWi-Fi接続は暗号化されません。必要な時間とネットワークに限定し、使い終わったらオフにします。

## 関連ページ

- [AGI Cockpitとは](https://agi-labo.com/tools/cockpit/docs)
- [タスク一覧](https://agi-labo.com/tools/cockpit/docs/tasks)
- [タスク詳細](https://agi-labo.com/tools/cockpit/docs/task-details)
- [Ask](https://agi-labo.com/tools/cockpit/docs/ask)
- [Autorun](https://agi-labo.com/tools/cockpit/docs/autorun)
