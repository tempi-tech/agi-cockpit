<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Referenceとサポート

タスク状態、設定、ショートカット、保存場所、更新、エージェント認証、Remote Access、App Surfaceの代表的なトラブル解決手順です。

> AGI Cockpit 4.61.0で2026-08-27に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/reference-and-support)

現在の状態を正確に読み、問題を小さな境界へ切り分けるためのReferenceです。最初にタスク、エージェント、接続先、OS、アプリバージョンを確認し、その後に該当する復旧手順へ進みます。

## タスク状態

| 状態 | 意味 | 次の確認 |
| --- | --- | --- |
| `running` | エージェントが実行中 | 現在のターン、ログ、キューを確認する |
| `waiting_confirmation` | 入力、承認、利用上限、再開を待つ | `waitingReason`と画面の案内を確認する |
| `completed` | タスクを完了済みに移した | 成果とWorktreeの保存状態を確認する |
| `error` | 起動またはランタイムが失敗した | `errorMessage`と診断情報を確認する |

`turn_complete`は一つの応答が終わったことを示し、タスク全体の完了ではありません。`needsResume`はタスクが未完了のまま実行プロセスを失い、再開操作が必要な補助情報です。

代表的な`waitingReason`は`turn_complete`、`permission`、`question`、`terminal_prompt`、`usage_limit`、`runtime_error`、`idle_timeout`、`unknown`です。CLIでは`cockpit task get <id>`で状態とsourceを確認します。

## 設定とショートカット

画面左下のアプリメニューから、外観、通知、ショートカット、エージェント、セットアップ、スキル、Browser Identity、履歴、Remote Access、Autorunを開けます。CLIでは`cockpit settings list`と`cockpit settings describe`で公開設定と値の制約を確認できます。

主要なショートカットにはQuick Task、タスク検索、送信キーがあります。送信キーはEnterまたはCmd/Ctrl+Enterを選択でき、Shift+Enterは改行です。ショートカットの競合やOS登録失敗は保存前に拒否されます。

設定を変更した後は、返された保存値を確認してください。数値設定は許容範囲へ丸められる場合があり、既存のAutorunは保存時のランタイム設定を保持するため、グローバル設定変更では自動更新されません。

## データ保存場所

| 種類 | 主な場所 |
| --- | --- |
| Cockpitのタスク、Autorun、Fleet、テンプレート、CLI、ログ | `~/.agi-tools/data/cockpit` |
| ユーザー共通のCLIランチャー | `~/.agi-tools/bin` |
| 永続ワークスペース | `~/.agi-tools/workspaces` |
| 作業プロジェクトとGit Worktree | タスク作成時に選んだ場所 |
| Electronの認証、添付、ブラウザープロファイル | OSのAGI Cockpitアプリデータ領域 |
| 認証tokenとAPIキー | OSのKeychainまたはkeyring |

`cockpit doctor`は現在接続するインスタンスとCLIランタイムを表示します。診断ログにはローカルパス、エージェント名、セッション状態が含まれる場合があるため、外部共有前に確認してください。

## アプリを更新できない

1. `cockpit update status`で`capability`と永続アップデーターログの要約を確認します。
2. macOSでは`cockpit update check --wait-download`で更新の確認とダウンロードを待ちます。
3. Linuxでは更新確認後、表示されたダウンロードページからAppImageまたは`.deb`を手動更新します。
4. Windows Store版はMicrosoft Storeの更新状態を確認します。
5. 自動インストールが応答しない場合、Cockpitは診断ログへ記録し、一度だけ再試行します。続く場合はダウンロードページから現行版を入れ直します。

更新フィードはCPUアーキテクチャ別です。Linux x64とarm64で異なる`latest-linux`フィードを使うため、OSだけでなくアーキテクチャも確認してください。更新ログは`~/.agi-tools/data/cockpit/logs/updater.jsonl`に保存され、2MBで2世代ローテーションします。

正確なコマンドは[`cockpit update` Reference](https://agi-labo.com/tools/cockpit/docs/cockpit-cli/reference/update)、公開済み変更は[バージョン履歴](https://agi-labo.com/tools/cockpit/docs/releases)を参照してください。

## エージェントが表示されない、起動しない

1. 画面左下の「セットアップ」で対象CLIの検出とバージョンを確認します。
2. `cockpit setup agent status <agent>`でコマンド、インストール済み版、利用可能な更新を確認します。
3. 設定の「エージェント」で起動コマンド、UIモード、アカウントの認証状態を確認します。
4. ネイティブUIの候補取得が失敗している場合、モデルや推論設定を固定せず、接続と認証を復旧して再取得します。
5. Terminal UIではネイティブUIのログイン案内を利用できないため、ターミナル内で対象CLIの認証を完了します。

利用上限ではタスクは`waiting_confirmation`と`usage_limit`になります。Autoで復旧できない場合は、利用可能な別プロファイルを追加または選択してから、新しい指示を送ります。

## タスクが再開しない

`needsResume`と`waitingReason`を確認し、`cockpit task resume <id>`で保存済みセッションを再開します。Terminalタスクでは新しいシェルが同じディレクトリで起動するため、以前のforeground processは戻りません。

別インスタンスへのCLI接続は自動転送されません。`cockpit doctor`の`instance`、runtime path、各portのlistenerと認証結果を確認し、正しいCockpitから同じコマンドを実行します。`instance_mismatch`では接続先を推測して再送しないでください。

## Remote Accessへ接続できない

1. DesktopでRemote Accessが実行中か確認します。
2. Tailscale限定では両端末が同じtailnetで認証済みか確認します。
3. HTTPSではMagicDNSとHTTPS Certificatesを有効にし、証明書を再取得します。
4. QRコードまたは接続URLがHTTPSであることを確認します。
5. ペアリングコードの期限切れ、3回失敗後の30秒ロックを確認します。
6. `cockpit remote-access status --verbose`でmode、scope、証明書、会員確認を切り分けます。

ローカルWi-Fiモードへ安易に切り替えず、証明書またはTailscale設定を復旧します。詳しくは[リモートアクセス](https://agi-labo.com/tools/cockpit/docs/remote-access)を参照してください。

## Browser Identityでサインインできない

タスクに割り当てられたIdentity名と色を確認します。別Identityでログインしても現在のタスクへ状態は移りません。macOSではChromeの対象タブを開き、`cockpit browser import-session`で選択したIdentityへCookieとlocalStorageを取り込めます。

パスキーが使えない場合は`cockpit browser diagnostics`でプラットフォーム認証器の状態と直近のWebAuthn試行を確認します。Linuxではセキュリティキーまたはパスワードを使います。

詳しくは[Browser Identity](https://agi-labo.com/tools/cockpit/docs/browser-identities)と[cockpit browser](https://agi-labo.com/tools/cockpit/docs/browser)を参照してください。

## App Surfaceへ接続できない

1. `cockpit app doctor`でADB、Xcode、制御runtime、現在の接続を確認します。
2. `cockpit app targets`で対象がonlineか、別タスクが使用中でないか確認します。
3. iOS SimulatorはXcodeで先にbootします。
4. Android実機はデバッグを有効にし、端末上の承認を受けます。
5. 接続後に`cockpit app status`でmirror、accessibility、input、keyboardのhealthを確認します。

`offline`または`stale`では最後の画面だけが残り、操作できません。対象やアプリをCockpitから起動・終了せず、プラットフォームのツールで準備してから再接続します。

詳しくは[App Surface](https://agi-labo.com/tools/cockpit/docs/app-surface)を参照してください。

## 問題を報告する

再現手順、期待した結果、実際の結果、AGI Cockpitのバージョン、OSとアーキテクチャ、対象エージェントとUIモード、エラーコードを揃えます。必要に応じて`cockpit doctor`、アップデーターログ、診断レポートの該当部分を添付します。

token、Cookie、ペアリングコード、ローカルの個人名、秘密のファイル内容は除去してください。既知の修正が公開済みか[バージョン履歴](https://agi-labo.com/tools/cockpit/docs/releases)を確認してから、AGI CockpitのGitHub Issueへ報告します。
