<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Hooks

タスクの完了、Ask、ホットキーなどをきっかけに処理を実行するHooksの使い方と、設定・実行履歴の確認方法を説明します。

> AGI Cockpit 4.77.0で2026-09-13に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/hooks)

Hooksは、タスクの完了やAskの作成など、Cockpitで起きたイベントをきっかけに、登録した処理を自動実行する機能です。「この出来事が起きたら、この処理をする」というルールをHookとして保存できます。ホットキーをきっかけにすることもできます。

## Autorunとの使い分け

| やりたいこと | 使う機能 | 例 |
| --- | --- | --- |
| 時刻や間隔に合わせて仕事を始める | [Autorun](https://agi-labo.com/tools/cockpit/docs/autorun) | 毎朝9時にレポートを作成する |
| 出来事に反応して処理を実行する | Hooks | タスクが完了したら結果をまとめるスクリプトを動かす |
| 複数タスクを依存関係に沿って進める | [Fleet](https://agi-labo.com/tools/cockpit/docs/fleet) | 調査が終わったら執筆とレビューへ進む |

組み合わせることもできます。たとえば、Autorunで始めたタスクの完了にHooksで反応したり、Fleet全体の終了をきっかけに後処理を実行したりできます。

## きっかけを選ぶ

| きっかけ | 主なイベント | 活用例 |
| --- | --- | --- |
| タスクの状態が変わる | `task.completed`、`task.waiting`、`task.error` | 完了後の処理や、確認待ち・エラーの通知を実行する |
| Askが作成・回答される | `ask.created`、`ask.resolved` | 外部サービスへ判断依頼や回答結果を連携する |
| Autorunが実行される | `autorun.triggered` | スケジュールの実行を記録する |
| Fleetが開始・終了する | `fleet.run.started`、`fleet.run.completed` | 一連の作業の終了後に後処理を実行する |
| アプリが起動・終了する | `app.ready`、`app.quit` | 起動時の準備処理を実行する |
| ショートカットキーを押す | `hotkey` | よく使う処理をキー操作で呼び出す |

`task.completed`はタスクが完了状態へ変わったときに発生します。同じ状態が再保存されても発生しません。`fleet.run.completed`は成功だけでなく失敗・キャンセルでも発生するため、成功時だけ実行する場合は状態のフィルターを付けます。

## エージェントに設定を依頼する

Cockpitのタスクから、きっかけ、対象、実行したい処理を伝えて設定を依頼できます。

> このプロジェクトのタスクが完了したら、指定した集計スクリプトを実行するHookを作って。子タスクは対象外にして、まず無効な状態で登録し、テスト結果を確認してから有効にして。

外部サービスとの連携には、そのサービスへ接続するスクリプトや認証設定が必要です。Hooksに登録するのは実際に実行するコマンドやスクリプトです。

## CLIで登録して試す

追加・変更・テストは`cockpit hooks`で行います。ここでは、タスク完了時にCockpitへメッセージを表示するHookを作ります。例はmacOSやLinuxのシェル向けです。

```bash
cockpit hooks add --event task.completed \
  --name completion-message \
  --disabled \
  --run 'cockpit display --text "${COCKPIT_TASK_NAME} completed"'
```

返された`hook.id`を以下の`<hookId>`に置き換えます。`<taskId>`には`cockpit task list`で確認したタスクIDを指定します。

```bash
cockpit hooks test <hookId> --task <taskId>
cockpit hooks enable <hookId>
```

まずテスト結果の`exitCode`が`0`で、表示内容が意図どおりであることを確認してから有効にします。`test`は無効なHookでもアクションを実行し、eventから起動した処理と同じ実行履歴へ記録します。通常のevent matching、debounce、queue、depth確認、circuit breakerの集計は迂回するため、有効化後は実際に一致するeventでも確認します。

## 対象と処理を絞り込む

プロジェクトやタスク名、エージェントなどで対象を絞れます。次の例は指定ディレクトリ配下のタスク完了に反応し、子タスクを除外します。パスは自分の環境に置き換えてください。

```bash
cockpit hooks add --event task.completed \
  --name project-summary \
  --directory /path/to/project \
  --no-child \
  --run-file /path/to/project/scripts/summarize.sh \
  --disabled
```

短いコマンドには`--run`、管理するスクリプトには`--run-file`を使います。複数のフィルターを指定すると、すべてに一致するイベントが対象になります。スクリプトはイベント情報を標準入力のJSONや`COCKPIT_TASK_ID`などの環境変数から受け取れます。

## 設定画面と実行履歴

1. 画面左下のアプリメニューから「設定」→「Hooks」を開きます。
2. Hookを新規作成するか、「編集」で名前、event、filter、action、timeout、debounce、hotkey、有効状態をその場で変更します。
3. 「テスト実行」でactionをすぐに実行できます。保存された正確な値を確認する場合は定義を展開します。
4. 実行履歴を開き、実行日時、イベント、終了コード、標準出力・標準エラーを確認します。
5. 不要になったHookは削除できます。削除しても既存の実行履歴は残ります。

設定画面のeditorはCLIと同じevent・filter catalogと同じ制御経路を使います。保存時には必須値と数値範囲を検証してから定義を置き換えます。

CLIからも状態と履歴を確認できます。

```bash
cockpit hooks list
cockpit hooks runs --hook <hookId> --limit 20
cockpit hooks disable <hookId>
```

実行されない場合は、Hookが有効か、イベントとフィルターが対象に一致しているかを確認します。ホットキーでは`cockpit hooks get <hookId>`の`registered`も確認してください。`false`ならショートカットは登録されていません。処理が失敗する場合は終了コードと標準エラーを確認します。実行時間の上限は既定で60秒で、`--timeout`で変更できます。

## 実行時の注意点

登録した処理は利用者のローカル権限で実行されます。エージェントの承認モードとは別に動くため、内容を確認したコマンドやスクリプトを登録してください。すべてのHooksコマンドで`--host <ホストまたはエイリアス>`を指定できます。接続先では会員向けRemote Accessを有効にしておく必要があります。リモートでの登録とテストは、接続先のコンピューターでシェルコードを実行します。ペアリング済みのBearer tokenが必須で、Tailscale限定接続では検証済みpeerまたはloopback接続も必要です。peerの信頼だけでは操作できません。このtokenはHookの登録・実行を含む接続先インスタンスの操作権限を持ちます。スクリプトのパスとディレクトリのフィルターは接続先のパスです。

`app.quit`で待機できる時間は最大5秒です。終了時に必ず完了させたい長い処理には使わないでください。イベントの連鎖による繰り返し実行には上限があるため、Hookが別のタスクを起動する場合は対象を絞ることも必要です。

## 関連ページ

- [Autorun](https://agi-labo.com/tools/cockpit/docs/autorun)：時刻や間隔に基づく自動化
- [Fleet](https://agi-labo.com/tools/cockpit/docs/fleet)：複数タスクの実行順序と依存関係
- [`cockpit hooks` Reference](https://agi-labo.com/tools/cockpit/docs/cockpit-cli/reference/hooks)：全イベント、フィルター、ホットキー、実行制御の詳細
- [セキュリティとデータ](https://agi-labo.com/tools/cockpit/docs/security-and-data)：実行権限とデータの保存範囲
