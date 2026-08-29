<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# cockpit CLI

AIエージェントと利用者がcockpit CLIからタスク、Ask、ブラウザー、App Surface、Autorun、Fleet、Hooks、設定を安全に操作する方法を説明します。

> AGI Cockpit 4.64.0で2026-08-30に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/cockpit-cli)

`cockpit`は、AIエージェントと利用者が実行中のAGI Cockpitを操作するための正式なCLIです。タスク、確認、成果表示、ブラウザー、App Surface、Autorun、Fleet、設定を、同じ状態と権限境界で扱います。

## セットアップと接続

初回セットアップまたは画面左下のアプリメニューにある「セットアップ」からCLIをインストールします。macOSとLinuxではシェルのPATH、WindowsではユーザーPATHへランチャーが追加されます。状態は次で確認できます。

```bash
cockpit setup status
cockpit doctor
cockpit help
```

Cockpitから起動したタスクには、正しいローカルインスタンスと呼び出し元タスクの接続情報が渡されます。別インスタンスへ誤送信しそうな場合は`instance_mismatch`で停止し、接続不能時に別のローカルインスタンスへ自動で切り替えません。

ローカルCLIは認証付きloopbackへ先に接続し、sandboxがloopbackまたはプロセス確認を制限している場合は、同じCockpitが渡したディレクトリのfile IPCへ自動で切り替えます。file IPCは一つのrequest IDを一度だけ書き、同じ応答を待つため、`task create`や`task send`を再送して重複実行しません。権限拡張は不要です。

`cockpit doctor`は`pidVisibility`、`transports.loopback`、`transports.fileIpc`、`effectiveTransport`を返します。終了コード7は、利用可能な両方の経路で認証できなかったことを示します。権限エラーと推測して再実行せず、失敗理由と対象instanceを確認してください。

## JSON結果を確認する

すべてのコマンドはJSONを返します。成功は`ok: true`、失敗は`ok: false`と`error`を持ち、安定したエラーでは`code`も返ります。終了コードだけでなく、`ok`、`code`、対象ID、状態、検証フィールドを確認します。

`--json`はすべてのコマンドで一貫して受け付けます。長い指示、Markdown、引用、バッククォート、`$`を含む内容はシェル引数へ埋め込まず、`--stdin`または`--instruction-file`、`--text-file`を使います。

## タスクを起動して監督する

単発作成には`task create`、別タスクを起動して最初の報告まで待つ場合は`task run`を使います。`turn_complete`は一つの応答ターンが終わったことを示し、タスク全体の完了ではありません。親子タスク、レポート、追加指示、完了の流れは[タスク管理（CLI）](https://agi-labo.com/tools/cockpit/docs/task-management)を参照してください。

```bash
cockpit task run --instruction "Review the change" --directory /path/to/repo
cockpit task wait <task-id> --since <report-seq>
cockpit task send <task-id> --text "Continue" --wait
```

`readyForNextPrompt`がfalseの確認待ちタスクへ新しい指示を重ねず、`waitingReason`を確認します。`permission`や`question`は進行中の確認、`usage_limit`はアカウント復旧、`needsResume`はプロセス再開が必要な状態です。

タスクの完了と削除は異なります。CLIの`task complete`は既定でGit Worktreeを削除し、維持する場合は`--keep-worktree`が必要です。削除や一括操作の前に対象IDと保存すべき成果を確認してください。

## Ask、display、HTMLを使い分ける

`cockpit ask`は人の回答を待ち、回答後に同じタスクを再開します。`cockpit display`は情報を前面表示するだけで回答を待ちません。`cockpit html`は生成したHTML Surfaceをタスクへ保存します。

Askを作成した後は、そのターンを終了して回答を待ちます。回答をpollしたり、回答に依存する処理を続けたりしません。Askへの回答はOSや外部サービスの実行権限そのものではありません。

## ブラウザーとApp Surfaceを操作する

Webページは`cockpit browser`、起動済みAndroidまたはiOS Simulatorは`cockpit app`を使います。ブラウザーではIdentityを確認し、クリックや送信にはURL、テキスト、要素状態、通信などの事後条件を付けます。App Surfaceではアクセシビリティラベルを優先し、操作後に新しいsnapshotまたは期待条件を確認します。

外部リンク、ファイルアップロード、物理端末、秘密入力には追加の安全境界があります。配送済みの入力を確認せず再送すると二重操作になる可能性があります。

実践手順は[cockpit browser](https://agi-labo.com/tools/cockpit/docs/browser)、[Browser Identity](https://agi-labo.com/tools/cockpit/docs/browser-identities)、[App Surface](https://agi-labo.com/tools/cockpit/docs/app-surface)を参照してください。

## AutorunとFleetを使う

`cockpit autorun`は一度、間隔、cronで新規タスクを起動するか、既存タスクへ指示を送ります。会員確認は作成時だけでなく実行時にも行われます。保存済みのランタイム設定を利用できなくなった場合、別設定へ無言で切り替えずAutorunを無効にします。

`cockpit fleet`は依存関係付きの複数タスクをRunとして実行します。YAMLの検証、gate、再試行、再開、Runタイトル、各ノードの進捗を扱います。単純な定期起動にはAutorun、依存する複数処理にはFleetを使います。実践手順は[Fleet](https://agi-labo.com/tools/cockpit/docs/fleet)、全構文は[`cockpit fleet` Reference](https://agi-labo.com/tools/cockpit/docs/cockpit-cli/reference/fleet)を参照してください。

## Hooksでイベントに反応する

`cockpit hooks`は、タスク、Ask、Autorun、Fleet Run、アプリのライフサイクル、ホットキーのイベントに対して、ローカルでシェルアクションを実行する宣言を登録します。フィルターでイベントのpayloadを絞り、`hooks runs`で失敗を確認し、依存する前にアクションを明示的にテストしてください。Hookの登録は任意のローカルコード実行にあたるため、このコマンド群は`--host`を拒否します。イベントpayload、安全策、正確なコマンド結果は[`cockpit hooks` Reference](https://agi-labo.com/tools/cockpit/docs/cockpit-cli/reference/hooks)を参照してください。

## ローカルとリモートを区別する

`task`と`autorun`の対応コマンドは、登録済みの端末aliasまたはホストを`--host`で指定できます。ホスト指定がない設定、ブラウザー、表示などのコマンドはローカルのCockpitだけを操作します。

Remote Accessの停止、Identityデータの消去、アカウント削除、CLIアンインストールなどは明示的な確認フラグを要求します。フラグがあることを実行許可の代わりにせず、利用者の指示と正確な対象を確認してください。

## コマンド別Reference

以下のReferenceは`electron/cockpit-docs/help`からビルド時に自動生成され、アプリ内の`cockpit help [topic]`と同じ正本を使います。Creative workflowsは公式ガイドの対象外ですが、CLI正本の正確性を保つため、既存の関連コマンドも生成Referenceには含まれます。
