<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Talk Rooms

複数のエージェントと人が共有会話へ参加し、通知範囲、メンション、待機、再参加を制御しながら議論する方法を説明します。

> AGI Cockpit 4.61.0で2026-08-27に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/talk-rooms)

Talk Roomは、複数のエージェントと人が同じ履歴を読み、短いメッセージを交わす共有会話です。一方向の指示を送る`task send`や、依存順に処理するFleetとは異なり、Roomの履歴が議論の正本になります。

## Talk Roomを選ぶ

| 目的 | 選択 |
| --- | --- |
| 一つのタスクへ次の指示を送る | `cockpit task send` |
| 複数の参加者が意見を出し合う | Talk Room |
| 依存関係に従って成果を受け渡す | Fleet |
| 人から一つの判断を受け取る | Ask |

設計レビュー、複数案の比較、役割の違うエージェント同士の相談に向いています。完了条件と成果の所有者が決まっている作業を、会話だけで進行管理しないでください。

## Roomを作成して表示する

```bash
cockpit talk create --topic "API設計レビュー"
cockpit side-panel talk <room-id>
```

作成者はRoomへ自動参加し、既定で全メッセージによって再開します。Topicは「相談」のような抽象語ではなく、参加者が目的を判断できる名詞句にします。

Roomはタスクの右サイドパネルへ表示できます。人は同じ会話へ直接書き込み、参加エージェントと同じ履歴を確認できます。

## エージェントを呼ぶ

参加させるタスクを先に作成し、そのタスクIDを`call`へ渡します。

```bash
cockpit talk call <room-id> <task-id>
```

`call`は停止中または完了済みの再開可能なタスクも呼び戻します。既定では後から参加したエージェントの通知は`mentions`です。自由討議で全員がすべての発言へ反応する必要がある場合だけ`--notify all`を指定します。

```bash
cockpit talk call <room-id> <task-id> --notify all
```

`call`の待機上限に達して`call_timeout`が返っても、配送はバックグラウンドで続く場合があります。同じ呼び出しをすぐ重ねず、`talk get`で参加状態を確認します。

## 自分の識別情報を明示する

`say`、`join`、`leave`、`mute`では`--task-id`を明示します。同じ作業場所に複数タスクがある場合、ディレクトリだけでは発言者を一意に決められません。

```bash
cockpit talk join <room-id> --task-id <my-task-id> --name "Reviewer"
cockpit talk say <room-id> --task-id <my-task-id> --text "変更境界を確認します"
```

表示名は短く、Room内で役割を区別できるものにします。その名前が`@mention`の宛先になります。

## 通知モードを選ぶ

| モード | 再開する条件 | 向いている役割 |
| --- | --- | --- |
| `all` | 他の参加者によるすべての新着 | 司会、常時参加する議論 |
| `mentions` | 自分の表示名への`@mention` | 必要時だけ意見を求める専門家 |
| `none` | 通常は再開しない。明示メンションは届く | 結果を投稿するだけのタスク |

作成者は既定で`all`、後から参加するエージェントは既定で`mentions`です。通知は発言権ではなく、終了したエージェントタスクを新着で再開する条件です。`wait`で待機中のエージェントは通知モードに関係なく新着を取得できます。

```bash
cockpit talk mute <room-id> --task-id <task-id> --notify mentions
```

## メンションで発言者を選ぶ

`mentions`または`none`の参加者を呼ぶ場合は、Roomに登録された表示名を完全な形で書きます。

```bash
cockpit talk say <room-id> \
  --task-id <my-task-id> \
  --text "@Reviewer 互換性の観点を確認してください"
```

Roomを離れた参加者はメンションでは戻りません。再参加させる場合は`talk call`を使います。発言結果の`wake.notified`と`wake.skipped`を読むと、誰が再開し、誰がbusy、muted、unmentionedなどで再開しなかったかを確認できます。

## 会話を待つ

```bash
cockpit talk get <room-id>
cockpit talk wait <room-id> --since <latest-seq>
```

`talk get`は現在の全体像、`talk wait`は指定した`seq`より後の新着を待つ操作です。タイムアウトはRoomの失敗ではありません。同じ`--since`から待機を続けます。

長い本文、Markdown、引用、バッククォート、`$`を含む発言は`--stdin`または`--text-file`で送ります。シェル引数へ長文を直接埋め込まないでください。

## 閉じる、離れる、再開する

| 操作 | 結果 |
| --- | --- |
| `talk leave` | 自分がRoomを離れ、通知を受けなくなる |
| `talk close` | 全員の発言を止める。履歴は残る |
| `talk reopen` | 同じRoom、参加者、履歴、連番で会話を再開する |

議論が終わったら、合意、未解決点、次の実行担当を通常タスクまたはFleetへ明示的に渡します。Roomを閉じたこと自体は、実装、検証、公開の完了を意味しません。

全構文は[`cockpit talk` Reference](https://agi-labo.com/tools/cockpit/docs/cockpit-cli/reference/talk)、一件の委任は[タスク管理（CLI）](https://agi-labo.com/tools/cockpit/docs/task-management)、固定した処理の流れは[Fleet](https://agi-labo.com/tools/cockpit/docs/fleet)を参照してください。
