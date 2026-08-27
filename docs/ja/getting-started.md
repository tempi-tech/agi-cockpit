<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# インストールと初回セットアップ

AGI Cockpitをインストールし、利用するエージェントとCockpit連携を準備して、最初のタスクを作れる状態にする手順です。

> AGI Cockpit 4.61.0で2026-08-27に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/getting-started)

この手順を終えると、AGI Cockpitを起動し、利用するエージェントとCockpit連携を準備して、最初のタスクを作れる状態になります。

## 1. AGI Cockpitをインストールする

[AGI Cockpitのダウンロードページ](https://agi-labo.com/tools/cockpit)を開き、利用するOSに合う配布形式を選びます。

| OS | 配布形式 | 対応 |
| --- | --- | --- |
| Windows | Microsoft Store | x64 / arm64 |
| macOS | `.dmg` | Apple Silicon |
| Linux | AppImage / `.deb` | x64 / arm64 |

WindowsではMicrosoft Storeからインストールします。macOSでは`.dmg`を開き、AGI CockpitをApplicationsへ移動します。Linuxでは配布されたAppImageまたは`.deb`を利用します。

既存タスクがない状態で初回起動すると、セットアップが開きます。アプリの案内、対応するタスクエージェントCLIの確認、Cockpit連携の設定を進めた後、AGIラボへのサインインを選びます。利用可能なエージェントが一つあれば続行できます。ゲスト利用で始める場合は最後の画面で「サインインせずに始める」を選び、Autorunや別端末からのリモートアクセスを使う場合はAGIラボ会員としてサインインします。後から画面左下のアプリメニューを開き、「セットアップ」の「初回セットアップ」から同じ流れを確認できます。

AGI Cockpit自身の認証情報とAPIキーは、OSのキーチェーンまたはキーリングなどの暗号化ストレージに保存します。暗号化ストレージを利用できない場合は平文保存へ切り替えず、保存を拒否して復旧方法を表示します。案内に従ってOSのキーチェーンまたはキーリングを有効にしてから、もう一度サインインしてください。Antigravityの名前付きアカウントは例外として、後述するプロファイル専用のアプリデータ領域で認証を分離します。

リモートアクセスでは、Tailscale限定モードが既定です。TailscaleのHTTPSを有効にした状態で証明書を利用できない場合、CockpitはHTTPへ自動降格せず、接続を開始しません。ローカルWi-Fiモードは通信が暗号化されないため、明示確認後にだけ有効になり、接続中は警告が表示されます。公共Wi-Fiでは使用しないでください。設定手順は[リモートアクセス](https://agi-labo.com/tools/cockpit/docs/remote-access)を参照してください。

## 2. 実行するエージェントを準備する

セットアップでは、Claude Code、Codex CLI、Grok Build、Antigravity CLI、Cursor CLI、Qoder CLIを確認します。各CLIのインストール状態と、Claude Code、Codex CLI、Grok Buildではサインイン状態も表示します。エージェントが見つからない場合は「インストール」を選びます。

新しいタスク画面には、利用可能なエージェントだけが表示されます。CLIを必要とするエージェントは、CockpitがそのCLIを検出できる場合だけ表示されます。Cockpit AgentとTerminalはCLI検出を必要としません。表示されないエージェントを追加する場合は、「エージェントを管理」から設定を開いてCLIをインストールするか、起動コマンドを確認します。導入後に新しいタスク画面へ戻り、エージェントが選択可能になれば準備完了です。

一度CLIの確認が完了すると、その結果が保存されます。次回からは保存済みのエージェント候補をすぐに操作でき、Cockpitはバックグラウンドで現在の状態を再確認します。候補の横に「エージェントを確認中」または「エージェントを確認できませんでした」のアイコンが出ている間は保存済み結果を表示しています。初回など保存済み結果がない場合だけ、確認が終わるまで読み込み表示になります。

連携設定では、検出済みのClaude Code、Codex CLI、Antigravity CLI、Cursor CLI、Qoder CLI、Grok Buildへ`cockpit`スキルを自動でインストールまたは更新します。同時に`cockpit`コマンドをインストールし、macOSとLinuxではシェル設定、Windowsではユーザー`PATH`へコマンドの場所を追加します。この処理は、検出した各エージェントのユーザー単位のスキルディレクトリと、該当するユーザー単位のパス設定を書き換えます。HTML Modeは任意で、別にインストールします。Cockpitは次回以降の起動時にも中核となるスキルとコマンドを更新します。後からエージェントを追加した場合は、画面左下のアプリメニューから「スキル」を開いて状態を確認できます。

Claude Code、Codex、Grok BuildをネイティブUIで使う場合は、未認証のまま開始しても、タスク詳細にログイン案内が表示されます。そこで認証を完了すると、Cockpitが最初の指示を同じタスクで再試行します。ターミナルUIやTerminalエージェントでは、この案内と自動再試行は使われません。表示されたターミナル内で対象CLIのログイン手順を完了してから、タスクを再開または作り直します。

AIエージェント側の契約と認証は、AGIラボへのサインインとは別です。AGIラボ会員としてサインインしていても、Claude Code、Codexなどはそれぞれのアカウントで利用可能な状態にする必要があります。

設定の「エージェント」では、Claude Code、Codex、Grok Build、Antigravity、Cursor、Qoderのアカウントプロファイルを作成できます。プロファイルごとに認証と接続設定が分離されます。新しいタスク、Autorun、Fleetのアカウントは既定で「Auto」になり、ログイン済みプロファイルの利用状況から実行するアカウントを選びます。特定のプロファイルまたはデフォルトアカウントへ固定することもでき、対応する実行中タスクでは後から切り替えられます。AntigravityのプロファイルはブラウザでGoogle OAuthを行い、OAuthトークン、会話、ログ、利用履歴をプロファイル専用のアプリデータ領域へ保存します。Cockpitは選択した領域でAgyを起動し、共有キーリングへのフォールバックを無効にします。開発用シェル資源と認証情報を含まないAntigravity設定は通常のホームと共有されます。macOSでは、分離プロファイルからホスト利用者のKeychain検索リストを参照できるため、GitHub CLIやGitの認証ヘルパーなどは既存のKeychain認証情報を利用できます。この参照によってAntigravityプロファイル専用のデータが共有されることはありません。

Claude、Codex、QoderのネイティブUIとCockpit Agentでは、組み込み設定に加えてカスタムシステムプロンプトを選べます。カスタムプロンプトは`cockpit system-prompt add`で登録すると、DesktopとPWAの新しいタスク画面およびAutorunのシステムプロンプト選択肢に表示されます。

```bash
cockpit system-prompt add reviewer --prompt "変更の正しさと分かりやすさをレビューしてください。"
cockpit system-prompt list
```

既定の`append`モードはCockpit標準のシステムプロンプトの後へ内容を追加し、組み込みのCockpit CLI知識を維持します。`--mode replace`は標準プロンプトを置き換え、その知識はインストール済みのCockpit skillからだけ利用できます。カスタムプロンプトは利用者所有のMarkdownとしてAGI Toolsのデータ領域に保存され、Master Agentファイルの再配備では上書きされません。プロンプト内容は選択したエージェントへ渡るため、認証情報や秘密を含めないでください。Cursor、Grok Build、Antigravity、Terminal、ターミナルUIではカスタムプロンプトを選べません。

## 3. AGI Cockpitを更新する

画面左下に表示されるアップデート通知、または同じ場所のアプリメニューにある「更新」から新しいバージョンを確認します。ダウンロードが完了した環境では、画面の案内から再起動してインストールします。Linuxでは、通知から現在の表示言語に合ったダウンロードページを開いて更新します。

続行に新しいバージョンが必要な画面では、アプリの表示言語に合ったリリースノートとダウンロード先が表示されます。日本語UIでは日本語、英語UIでは英語の案内を確認してから更新してください。

アップデート確認が一時的なネットワークまたはサーバーの問題で失敗すると、Cockpitは一度自動で再試行します。それでも確認できない場合は、原因に応じた案内と「再試行」「手動で更新」を表示します。更新データを検証できない場合は自動適用を続けず、手動更新の案内に従います。

パッケージ版macOSでインストールを要求した後も5分以内にアプリが終了しない場合、Cockpitは応答停止を記録し、インストーラーへの要求を一度だけ再試行します。更新確認、ダウンロード、検証、インストール要求、再起動の診断ログは`~/.agi-tools/data/cockpit/logs/updater.jsonl`へ保存され、その要約は`cockpit update status`と診断情報のエクスポートでも確認できます。再試行後も現在のバージョンで起動する場合は、ログの最後のインストール結果を確認し、「手動で更新」から配布ページを開いてください。

## 4. セットアップを完了できない場合

| 表示または状態 | 確認すること |
| --- | --- |
| 使いたいエージェントが一覧にない | 「エージェントを管理」で対象CLIをインストールするか、設定の起動コマンドを確認する |
| ネイティブUIでログインが必要 | タスク詳細のログイン案内を完了し、同じ指示の自動再試行を待つ |
| ターミナルUIまたはTerminalでログインが必要 | ターミナル内で対象CLIのログインを完了し、タスクを再開または作り直す |

## 関連ページ

- [最初のタスク](https://agi-labo.com/tools/cockpit/docs/first-task)
- [タスク一覧](https://agi-labo.com/tools/cockpit/docs/tasks)
- [リモートアクセス](https://agi-labo.com/tools/cockpit/docs/remote-access)
- [Ask](https://agi-labo.com/tools/cockpit/docs/ask)
- [Autorun](https://agi-labo.com/tools/cockpit/docs/autorun)
- [バージョン履歴](https://agi-labo.com/tools/cockpit/docs/releases)
