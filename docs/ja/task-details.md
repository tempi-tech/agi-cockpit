<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# タスク詳細

選択したタスクの会話、追加指示、キュー、割り込み、再開、アカウント、添付、エラーを扱う方法です。

> AGI Cockpit 4.61.0で2026-08-27に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/task-details)

タスク詳細は、[タスク一覧](https://agi-labo.com/tools/cockpit/docs/tasks)で選んだ仕事を理解し、次の指示や判断を返す場所です。会話、進捗、確認要求、入力欄と、そのタスクに紐づく右サイドパネルを扱います。

## 追加指示、キュー、割り込み、再開

- **追加指示**: 確認待ちのタスクへ次のターンを送ります。
- **キュー**: 実行中に送った指示を、現在のターンが終わった後に順番に実行します。
- **現在のターンに割り込み**: 実行中の処理を止め、入力した指示へ切り替えます。
- **再開**: アプリ再起動などでプロセスが止まった未完了タスクを、保存済みセッションへ接続し直します。

Terminalタスクは以前のシェルプロセスを復元できないため、再開すると同じディレクトリで新しいシェルを起動します。Cursor、Qoder、Grok BuildのネイティブUIは、対応する保存済み会話へ再接続します。

実行中のターンはEscapeで停止できます。複数のタスクペインがある場合は、フォーカスのあるペインだけが対象です。

## 実行アカウントを確認する

Claude、Codex、Grok Build、Antigravity、Cursor、Qoderの新しいタスクでは、アカウントの既定は「Auto」です。Autoはログイン済みアカウントの利用状況から実行先を選び、利用上限またはプラン制限を検出すると、利用可能な別アカウントへ切り替えて保存済みセッションを続行します。

DesktopとPWAでは入力欄付近の「Auto・アカウント名」で現在の実行先を確認できます。アカウントメニューからAuto、デフォルトアカウント、名前付きプロファイルを選べます。手動切り替えでは現在の実行を停止し、保存済み会話を選択したプロファイルへ移して再開します。

利用可能なアカウントがない、利用状況を確認できない、切り替えまたは再開に失敗した場合、タスクは`waiting_confirmation`と`usage_limit`で停止します。案内に従って別アカウントを選ぶか、表示されたリセット時刻以降に再試行します。

## メッセージを入力する

Desktopの「送信キー」設定では、EnterまたはCmd/Ctrl+Enterを送信に割り当てられます。Shift+Enterはどちらの設定でも改行です。利用可能なDesktop環境では、入力欄のマイクから90秒までのローカル音声入力を使えます。端末内で文字起こしされた内容を確認してから送信してください。

PWAで実行中のタスクへ送るメッセージはキューへ入り、現在の返答後に送信されます。送信前ならキューから取り除けます。Claude、Codex、Grok BuildのネイティブUIでは、PWAから`/compact`だけを送ると会話コンテキストを圧縮します。

## 会話を読み、引用する

チャット本文を選択して「選択範囲を引用」を実行すると、Markdownの引用として入力欄へ追加できます。進捗表示が更新されても、選択中の範囲は維持されます。

別のCockpitタスクから届いたメッセージには「別タスクから送信」と送信元のタスク名または短縮IDが表示されます。送信元が一覧に残っている場合は、その名前から移動できます。親子関係だけでは結果の自動送信や親タスクの自動再開は起きません。

PWAで会話の末尾から離れると下向き矢印が表示されます。選ぶと最新メッセージへ戻り、新しい出力を追う状態になります。

## エラーとツール実行を確認する

メッセージのコピーボタンは、表示本文とエラー詳細をまとめてコピーします。診断情報にはローカルのファイルパス、セッションログ、アカウント情報が含まれる場合があるため、外部へ共有する前に確認してください。

複数のツール実行が一つのグループにまとまっている場合、一部だけが失敗すると見出しに失敗件数が表示されます。成功した項目があっても、失敗した操作とその影響を確認します。

## ファイルを確認・編集する

タスクのファイルボタンは右サイドパネルへ対象を開きます。書き込み可能な通常テキストファイルは1MB以下なら鉛筆ボタンから編集でき、変更は自動保存されます。Cockpit内の取り消し操作はないため、パスとGit管理範囲を確認してください。

差分、HTML Surface、ブラウザー、App Surface、ターミナルの役割は[成果とツール](https://agi-labo.com/tools/cockpit/docs/results-and-tools)で確認できます。Web操作は[cockpit browser](https://agi-labo.com/tools/cockpit/docs/browser)、接続したモバイル画面は[App Surface](https://agi-labo.com/tools/cockpit/docs/app-surface)を参照してください。

## ファイルを添付する

DesktopとPWAでは、画像、テキスト、ソースコード、JSON、CSV、PDF、音声、動画、Office文書を添付できます。対応形式をエージェントへ直接渡せない場合は、ローカルパスと名前、MIME、サイズのmetadataを渡します。

一つのメッセージへ最大8ファイル、1ファイル512MB、合計1GBまで添付できます。JSONは1ファイル25MBまでで、アーカイブと実行形式には対応しません。Cockpitは拡張子、MIME type、実サイズ、内容を検証し、元のファイル名ではなくランダムな名前で管理領域へ保存します。

上限はメッセージ単位です。以前送った添付は次のメッセージの8件に含みません。クリップボードにプレーンテキストがある場合はテキストを挿入し、不要な画像表現を添付しません。

添付ファイルの名前や内容を自動的な指示として信頼しません。どのファイルを何のために使うか、期待する結果を本文で明示してください。Cockpitは管理領域内の安全な形式だけを関連アプリで開き、実行形式、管理外path、remote `file` URL、実行可能なHTML・SVGを含み得るdata URLは直接起動しません。

## CLIから続行する

```bash
cockpit task get <id>
cockpit task account <id> auto
cockpit task send <id> --text "追加指示" --wait
cat follow-up.md | cockpit task send <id> --stdin --wait
cockpit task send <id> --text-file follow-up.md --wait
cockpit task wait <id> --since <seq>
```

複数行やシェルが解釈し得る内容は`--stdin`または`--text-file`で渡します。`task wait`は保存済みreportまたは指定sequenceより後のreportを返し、別タスクへ指示を注入しません。

## 関連ページ

- [タスク一覧](https://agi-labo.com/tools/cockpit/docs/tasks)
- [Ask](https://agi-labo.com/tools/cockpit/docs/ask)
- [Browser Identity](https://agi-labo.com/tools/cockpit/docs/browser-identities)
- [成果とツール](https://agi-labo.com/tools/cockpit/docs/results-and-tools)
