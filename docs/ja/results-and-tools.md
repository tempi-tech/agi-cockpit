<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# 成果とツール

タスクの差分、ファイル、HTML Surface、ターミナル、ログを安全に確認し、専門ページへ進む方法を説明します。

> AGI Cockpit 4.61.0で2026-08-27に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/results-and-tools)

タスクの会話だけでなく、変更されたコード、ファイル、レポート、実行中のプロセスを同じタスクから確認できます。このページでは成果確認に共通する面を整理します。Webページの操作は[cockpit browser](https://agi-labo.com/tools/cockpit/docs/browser)、接続したモバイル画面は[App Surface](https://agi-labo.com/tools/cockpit/docs/app-surface)で扱います。

## 右サイドパネルを使う

タスク詳細の右サイドパネルは、会話を離れずに補助情報と操作面を開く場所です。差分、ファイル、プロジェクトエクスプローラー、HTML Surface、アプリ内ブラウザー、App Surface、サイドターミナル、バックグラウンドログ、子タスク、Talk Roomなどが、必要な文脈で表示されます。

パネル上部の種類と対象パスを確認してから操作してください。パネルを閉じてもタスクやブラウザーセッションは終了しません。幅を変更した場合は表示状態として保存され、狭い幅では本文と表が横スクロール可能な表示へ切り替わります。

## 差分とファイルを確認する

差分はGitが認識する変更を確認する面です。追加、変更、削除をファイル単位で確認し、エージェントの説明だけで完了を判断せず、実際の変更内容と対象範囲を照合します。Git管理外の一時ファイルやプロジェクト外のファイルは差分に含まれない場合があります。

ファイルプレビューはテキスト、画像、音声、動画、PDFなど対応形式を表示します。書き込み可能な既存の通常テキストファイルは、1MB以下であれば鉛筆ボタンから直接編集できます。編集は自動保存され、Cockpit内の取り消し操作はありません。パスとGit管理範囲を確認してから編集してください。

プロジェクトエクスプローラーは作業場所の階層をたどる面です。表示更新後も選択中のファイルを維持し、ファイルを選ぶとプレビューへ移ります。秘密情報を含む可能性があるファイルを外部へ共有する前に、内容とパスを確認してください。

## HTML ModeとHTML Surfaceを区別する

HTML Modeは、エージェントが最終結果を対話可能なHTMLとして構成するためのスキルです。HTML Surfaceは、そのHTMLをタスクへ保存し、右サイドパネルで表示する製品面です。

HTML Surfaceは一般のWebブラウザーではありません。エージェントが作ったレポート、比較表、ダッシュボードなどを表示する用途に使い、外部サイトの移動やフォーム操作にはアプリ内ブラウザーを使います。HTML内のHTTPまたはHTTPSリンクは外部ブラウザーへ渡され、それ以外のURLスキームは無視されます。

CLIからは`cockpit html show --stdin`でHTMLを保存し、`cockpit side-panel html`で表示できます。正確な契約は[`cockpit html` Reference](https://agi-labo.com/tools/cockpit/docs/cockpit-cli/reference/html)を参照してください。

## 専門の操作面へ進む

アプリ内ブラウザーは、実際のWebページを人とエージェントが同じタブで扱う面です。ページ状態、意味のある要素名、操作後の事後条件まで含む手順は[cockpit browser](https://agi-labo.com/tools/cockpit/docs/browser)、ログイン状態の分離は[Browser Identity](https://agi-labo.com/tools/cockpit/docs/browser-identities)を参照してください。

App Surfaceは、起動済みAndroidまたはiOS Simulatorを一つのタスクへ接続する面です。準備、接続、アクセシビリティ操作、座標、復旧、安全境界は[App Surface](https://agi-labo.com/tools/cockpit/docs/app-surface)を参照してください。

## サイドターミナルとバックグラウンドログを使う

サイドターミナルは、タスクのエージェントプロセスとは別のシェルセッションです。コマンドを実行する前に、セッション名、現在の作業ディレクトリ、対象ホストを確認してください。ターミナルを閉じるとそのシェルは終了しますが、タスク本体の完了とは別です。

バックグラウンドログは、エージェントが開始した長時間プロセスの出力を確認する面です。ログが表示されていることだけで処理完了とは判断せず、終了コード、生成物、待受ポート、テスト結果など、依頼に合う成功条件を確認します。

CLIからサイドパネルとターミナルを扱う場合は[`cockpit side-panel` Reference](https://agi-labo.com/tools/cockpit/docs/cockpit-cli/reference/side-panel)を参照してください。

## 文脈依存のパネルを使う

子タスクパネルは親タスクに属する子タスクの状態と報告を確認します。親子関係とreportの扱いは[タスク管理（CLI）](https://agi-labo.com/tools/cockpit/docs/task-management)を参照してください。Talk Roomパネルは参加者同士の共有会話を表示します。参加、通知、メンションは[Talk Rooms](https://agi-labo.com/tools/cockpit/docs/talk-rooms)を参照してください。

スキル詳細は、選択したスキルの説明と利用条件を確認する面です。スキルの指示がタスクの権限を拡張するわけではありません。公開、削除、外部送信などは通常の承認境界に従います。

## 成果確認を完了する

次の順で確認すると、表示しただけの状態を完了と誤認しにくくなります。

1. タスクの目的と成功条件を読み直す
2. 差分とGit管理外の生成物を確認する
3. テスト、ビルド、画面、通信など該当する証拠を確認する
4. 秘密情報、破壊的変更、外部公開の有無を確認する
5. 必要な成果を保存または共有してからタスクを完了する

タスクの状態、完了、削除は[タスク一覧](https://agi-labo.com/tools/cockpit/docs/tasks)、追加指示と再開は[タスク詳細](https://agi-labo.com/tools/cockpit/docs/task-details)、データ境界は[セキュリティとデータ](https://agi-labo.com/tools/cockpit/docs/security-and-data)を参照してください。
