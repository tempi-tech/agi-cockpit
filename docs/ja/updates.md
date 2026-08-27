<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# AGI Cockpitを更新する

Windows、macOS、LinuxでAGI Cockpitの更新を確認・適用し、失敗した場合に更新状態と診断ログを確認する手順です。

> AGI Cockpit 4.61.0で2026-08-27に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/updates)

AGI Cockpitの更新方法は配布形式によって異なります。この手順では、現在の環境に合う更新方法を確認し、更新後の状態を確かめます。

## 1. 更新方法を確認する

画面左下のアップデート通知、またはアプリメニューの「更新」を開きます。CLIでは次のコマンドで現在の更新能力と状態を確認できます。

```bash
cockpit update status
```

| 環境 | 更新方法 |
| --- | --- |
| macOSパッケージ版 | Cockpitが更新を確認、ダウンロード、インストールする |
| Linuxパッケージ版 | Cockpitで更新を確認し、AppImageまたは`.deb`を手動で入れ替える |
| Windows Store版 | Microsoft Storeで更新する |
| 開発版または未対応ビルド | 配布ページから対応するパッケージを確認する |

## 2. 更新を適用する

macOSでは、更新のダウンロード完了後に画面の案内から再起動してインストールします。CLIから実行する場合は、更新のダウンロードを待ってから、実行中タスクが終了することを確認してインストールします。

```bash
cockpit update check --wait-download
cockpit update install --confirm
```

`cockpit update install --confirm`が成功するとAGI Cockpitが再起動し、実行中のすべてのタスクが終了します。

Linuxでは「手動で更新」または更新通知から表示言語に合うダウンロードページを開き、現在のCPUアーキテクチャに合うAppImageまたは`.deb`へ更新します。WindowsではMicrosoft StoreのライブラリからAGI Cockpitを更新します。

## 3. 更新結果を確認する

再起動後に「更新」を開くか、`cockpit update status`を実行し、現在の状態と最後のインストール結果を確認します。公開済みの変更は[バージョン履歴](https://agi-labo.com/tools/cockpit/docs/releases)で確認できます。

## 更新できない場合

一時的なネットワークまたはサーバー障害では、Cockpitが更新確認を一度再試行します。それでも失敗する場合は「再試行」または「手動で更新」を選びます。更新データを検証できない場合、Cockpitは自動適用を続けません。

macOSでインストール要求後も5分以内にアプリが終了しない場合は、応答停止を記録してインストール要求を一度だけ再試行します。更新確認、ダウンロード、検証、インストール、再起動の診断ログは`~/.agi-tools/data/cockpit/logs/updater.jsonl`に保存されます。

`cockpit update status`で最後のエラーとインストール結果を確認し、解決しない場合は[Referenceとサポート](https://agi-labo.com/tools/cockpit/docs/reference-and-support)の報告手順に従います。

## 関連ページ

- [`cockpit update` Reference](https://agi-labo.com/tools/cockpit/docs/cockpit-cli/reference/update)
- [Referenceとサポート](https://agi-labo.com/tools/cockpit/docs/reference-and-support)
- [バージョン履歴](https://agi-labo.com/tools/cockpit/docs/releases)
- [AGI Cockpitをインストールする](https://agi-labo.com/tools/cockpit/docs/getting-started)
