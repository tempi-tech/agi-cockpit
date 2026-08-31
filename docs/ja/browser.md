<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# cockpit browser

タスクのアプリ内ブラウザーでWebページを開き、人とエージェントが同じタブを安全に確認・操作・検証する方法です。

> AGI Cockpit 4.66.0で2026-09-01に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/browser)

`cockpit browser`は、タスク単位のアプリ内ブラウザーで実際のWebページを開き、DOM、画像、操作結果を確認するための正式な操作面です。表示専用ではなく、クリック、入力、選択、アップロード、貼り付け、キー操作、スクロールまで行えます。

[HTML Surface](https://agi-labo.com/tools/cockpit/docs/results-and-tools#html-modeとhtml-surfaceを区別する)はエージェントが作ったレポートを表示する面です。外部サイトの移動、ログイン、フォーム操作、Web UI検証にはアプリ内ブラウザーを使います。

## 人とエージェントが同じページを使う

各タブには正本となるページインスタンスが一つあり、利用者がサイドパネルで見るページとエージェントが操作するページは同じです。タスク、タブ、右サイドパネルを切り替えるとページはウィンドウから外れますが、再読み込みされません。未送信フォーム、スクロール位置、SPA状態、popupやOAuthの文脈を維持します。

ブラウザーsessionとnavigation履歴は、タスクの完了・再開とアプリ再起動を越えて保存されます。再起動後のフォーム値とスクロール位置はbest effortで復元され、OSの資格情報ストアを利用できる場合は保存データを暗号化します。

パネルを隠したparked tabも表示せずに操作できます。macOSのSpaceを切り替えたり、黒い独立ウィンドウを表示したりせず、同じページへ入力します。

## ページを開く

```bash
cockpit browser open https://example.com --json
cockpit browser tabs --summary --json
cockpit browser show --json
```

Cockpitタスク内では、sessionやtask IDを省略すると現在のタスクを使います。`open`は直近のsessionと選択中のタブを再利用します。未送信内容を残す場合は`--new-tab`を付けます。

```bash
cockpit browser open https://example.com/form --new-tab --json
cockpit browser goto <tabId> https://example.com/next --json
cockpit browser reload <tabId> --json
```

`tabs --summary`は、選択状態、本文先頭のpreview、読み込み時間、renderer応答、直近のnavigation errorを返します。popupまたはページ側の`window.close()`もCockpitのタブ状態へ反映されます。

## snapshotとscreenshotを使い分ける

```bash
cockpit browser snapshot <tabId> --json
cockpit browser snapshot <tabId> --wait-for-text "Ready" --timeout 10000 --json
cockpit browser screenshot <tabId> --full-page --output ./page.png --json
```

`snapshot`は表示テキスト、link、button、input、role、accessible name、selectorを返します。操作対象を特定し、状態を機械的に確認するときに使います。`screenshot`は見た目、配置、画像、canvasなどの視覚確認に使います。

locatorはopen shadow rootをたどります。通常のCSS selectorに加え、`host >>> inner`でshadow境界を明示できます。closed shadow rootはページ外から参照できないため、必要ならscreenshot座標で操作します。

## 意味のある名前で操作する

```bash
cockpit browser click <tabId> --role button --name "Submit" --json
cockpit browser type <tabId> --role textbox --name "Email" --text "name@example.com" --json
cockpit browser select <tabId> --selector "#plan" --option-text "Team" --json
cockpit browser scroll-into-view <tabId> --role button --name "Continue" --json
```

可能なら`--role`と`--name`を使い、次に安定したCSS selector、最後にviewport座標を使います。accessible nameは`aria-labelledby`、`aria-label`、label、alt、value、本文、titleから計算されます。既定は大文字・小文字を区別しない完全一致で、`--name-match prefix|contains`を明示できます。

候補がない、複数ある、すべて非表示の場合は、それぞれ`browser_target_not_found`、`browser_target_ambiguous`、`browser_target_not_visible`として候補情報を返します。曖昧なまま最初の候補を操作しません。

## 操作結果を事後条件で検証する

```bash
cockpit browser click <tabId> --role button --name "Save" \
  --wait-for-text "Saved" --timeout 10000 --json

cockpit browser click <tabId> --selector "#checkout" \
  --wait-for-url "*/complete" \
  --wait-for-response-url "*/api/orders*" \
  --wait-for-response-status "200-299" --json
```

クリック結果の`changed`と`changeSignals`は、URL、DOM、フォーム値など観測できた変化を示します。`changed: false`は成功の証明ではありません。URL、表示テキスト、selector状態、新しいタブ、通信statusなど、依頼の成功条件に合うpostconditionを付けます。

`eventDispatched: false`でも`inputDelivered: true`の場合、イベント伝播が止められていても操作は作用した可能性があります。二重送信を避けるため、同じクリックをすぐ繰り返さず、snapshotで現在のページ状態を確認します。

通信の検証結果はmethod、status、query値とhashをmaskしたURLだけを返します。request・responseのbody、header、payloadは返しません。

## 入力、貼り付け、アップロード、dialog

```bash
cockpit browser paste <tabId> --selector "[contenteditable]" --text "長い本文" --json
cockpit browser upload <tabId> --role button --name "Attach a file" --path ./build.zip --json
cockpit browser press <tabId> --selector "#search" --key Enter --json
cockpit browser dismiss-dialog <tabId> --button-text "OK" --json
```

`type`は対象の値を置き換えて最終値を検証します。`paste`は実際のclipboard paste eventを送り、指定テキストが反映されたかを検証して、元のclipboard表現を復元します。`upload`はOSのfile pickerを開かず、file inputまたはそれを含むdropzoneへファイルを設定します。

`dismiss-dialog`はJavaScript dialogを先に扱い、なければ表示中のHTML dialogを操作します。`--reject`で否定側を選べます。送信、購入、公開、削除など外部状態を変える操作は、postconditionが設定できても利用者の承認境界を越えません。

## 読み込み不良とrenderer停止を復旧する

`tabs`の`health`で次を区別します。

| 値 | 意味 |
| --- | --- |
| `loadingDurationMs` | Chromiumが読み込み中と報告している時間 |
| `rendererResponsive` | ページrendererが短いJavaScript pingへ応答したか |
| `lastNavigationError` | 直近のmain-frame読み込み失敗 |

siteが遅いだけなら待機し、navigation errorならURLや接続を確認します。rendererが停止した場合は、同じtab ID、URL、session、Browser Identityを保ったままrendererだけを作り直せます。

```bash
cockpit browser tab recreate <tabId> --json
```

## ログインと安全境界

CookieやlocalStorageなどの分離、task・Autorunへの割り当て、Chrome session取込、消去と削除は[Browser Identity](https://agi-labo.com/tools/cockpit/docs/browser-identities)を参照してください。

パスキーは署名済みmacOS版でTouch ID、WindowsでWindows Helloを利用できます。macOSのCockpitで使えるのは、アプリ内ブラウザーから登録したパスキーです。Safari、Chrome、iCloudキーチェーンで登録済みのパスキーを直接利用することはできません。

Touch IDに一致するパスキーがない場合、ブラウザーパネルは理由と、新しいパスキーの登録、パスワード、`cockpit browser import-session`という代替手段を表示します。`cockpit browser diagnostics`では直近の試行を`no-matching-credential`として確認できます。`import-session`はmacOSのChromeからログイン状態を取り込む機能で、パスキー自体は取り込みません。

Linuxにはplatform authenticatorがないため、roaming security keyまたはpasswordを使います。promptが出ない場合は`cockpit browser diagnostics`でplatform authenticatorと直近のWebAuthn試行を確認します。

アプリ内ブラウザーからOSへ渡せる外部linkはHTTPとHTTPSです。`mailto`は利用者自身の操作と明示確認が揃った場合だけ開き、ほかのschemeは拒否します。エージェントの自動操作は利用者自身の操作として扱われません。

## 関連ページ

- [Browser Identity](https://agi-labo.com/tools/cockpit/docs/browser-identities)
- [`cockpit browser` Reference](https://agi-labo.com/tools/cockpit/docs/cockpit-cli/reference/browser)
- [成果とツール](https://agi-labo.com/tools/cockpit/docs/results-and-tools)
- [セキュリティとデータ](https://agi-labo.com/tools/cockpit/docs/security-and-data)
