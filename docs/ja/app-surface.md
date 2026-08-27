<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# App Surface

起動済みのAndroid端末またはiOS Simulatorをタスクへ接続し、画面を安全に確認・操作・切断する方法です。

> AGI Cockpit 4.61.0で2026-08-27に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/app-surface)

App Surfaceは、起動済みのAndroidエミュレーター、Android実機、または起動済みiOS Simulatorを一つのタスクへ接続し、人とエージェントが同じ画面を確認・操作する面です。

Cockpitは対象を起動・終了せず、アプリのインストール、起動、終了も行いません。同じ対象を複数タスクへ同時接続できません。

## 対応対象を準備する

AndroidではADBを利用できる状態にし、エミュレーターを起動するか、USBまたはWi-Fiデバッグを有効にした実機を接続します。`unauthorized`と表示された場合は、端末側のデバッグ許可へ応答してください。

iOSではXcodeをインストールし、対象Simulatorをあらかじめ起動します。停止中のSimulatorは`offline`として表示され、Cockpitは自動起動しません。

```bash
cockpit app doctor
cockpit app targets
```

`doctor`はADB、Xcode、組み込み制御runtime、オンライン対象、現在の接続を検査し、復旧に外部コマンドが必要な場合は`nextAction`を返します。`targets`はplatform、emulator・Simulator・実機の種別、接続方式、online・offline・unauthorized状態、機能、使用中なら所有タスクを表示します。

## タスクへ接続する

```bash
cockpit app attach <target-id>
cockpit app status
cockpit app show
```

`attach`は、接続直後にruntimeとアクセシビリティの初回観測を完了してから返ります。Android実機への初回接続はCockpit Askによる明示承認が必要です。エミュレーターとSimulatorは承認を要求しません。

`status.health`で対象、mirror、アクセシビリティ、入力、keyboard、foreground app、直近の構造化エラー、操作可能性を確認します。`show`と`hide`はサイドパネルの表示だけを切り替え、非表示中もmirrorと接続は続きます。

## 画面を観測する

```bash
cockpit app snapshot
cockpit app screenshot --output ./artifacts/app.png
cockpit app wait --text "Welcome" --timeout 10000 --require
cockpit app wait --label "Continue" --role button
```

`snapshot`はアクセシビリティツリーと`@e1`のような一時参照を返します。tap、fill、swipe、key操作を行うと参照は無効になるため、次の操作前に新しいsnapshotを取得します。

`screenshot`はPNGを返し、viewportに`coordinateSpace`と`screenshotScale`を含みます。`wait`はtext、要素、接続状態、Android package、keyboard状態などを組み合わせられます。通常のtimeoutは`timedOut: true`を返し、`--require`を付けると`condition_not_met`として失敗します。

## ラベルを優先して操作する

```bash
cockpit app tap --label "Continue" --role button --expect-text "Home"
cockpit app fill --label "Mission Notes" --role textbox $'東京 🗼\nSecond line'
cockpit app swipe up
cockpit app press back
cockpit app keyboard hide
```

対象は、アクセシビリティの`--label`と任意の`--role`、新しいsnapshotの参照、座標の順で指定します。ラベルは既定で大文字・小文字を区別する完全一致です。platformがplaceholderなどを追加する場合だけ`--label-match prefix`または`contains`を使います。0件は`element_not_found`、複数件は`ambiguous_element`として失敗します。

Androidの座標は画面pixelで、screenshotのpixelと一致します。iOS Simulatorの操作座標は論理pointです。PNG上の位置へ変換するときは`screenshotScale`を掛けます。

`dispatched: true`はplatformが入力を受け付けたことを示すだけで、アプリが反応した証明ではありません。tap、swipe、pressでは`--expect-text`または`--expect-not-text`を付けるか、操作後に新しいsnapshotを取得します。

`fill`は入力後に新しいアクセシビリティsnapshotを読み、Unicodeと改行を含む値の完全一致を確認します。secure fieldは値をmaskし、`fill`を`secure_field`で拒否します。keyboardを隠せたか検証できない場合も成功扱いにしません。

## 切断と復旧

```bash
cockpit app hide
cockpit app detach
```

`offline`または`stale`になったSurfaceは最後の画面を残しますが、操作を無効にします。Desktopの「対象に再接続」またはCLIの再接続で健康状態を確認してください。タスクの完了・削除または`detach`は対象を切断しますが、アプリを終了しません。

エラーは安全境界として扱います。実機承認を迂回する、secure fieldへ入力する、別タスクが使用中の対象を奪う、依頼されていないアプリ起動・終了を行う、といった再試行はしません。`actionId`がある失敗は、JSONと直近のscreenshotを一緒に残すと診断できます。

## 関連ページ

- [成果とツール](https://agi-labo.com/tools/cockpit/docs/results-and-tools)
- [`cockpit app` Reference](https://agi-labo.com/tools/cockpit/docs/cockpit-cli/reference/app)
- [Ask](https://agi-labo.com/tools/cockpit/docs/ask)
- [セキュリティとデータ](https://agi-labo.com/tools/cockpit/docs/security-and-data)
