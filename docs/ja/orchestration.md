<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# オーケストレーション

親子タスク、Master Agent、task runとreport、Fleet、テンプレート、Talk Roomsを使って複数の仕事を安全に連携する方法を説明します。

> AGI Cockpit 4.61.0で2026-08-27に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/orchestration)

オーケストレーションは、一つの目的を複数の独立したタスクへ分け、結果を明示的に集めて次の判断へつなぐ方法です。指定時刻に仕事を始めるAutorunや、人へ判断を委ねるAskとは責務が異なります。

## 複数タスクへ分ける

同時に進めても互いのファイルや判断を壊さない仕事だけを並列にします。同じ作業場所を共有するタスクは同じファイルを変更できるため、担当範囲を分けるか、Git Worktreeを使って作業場所を分離します。

分割するときは、各タスクへ目的、対象範囲、完了条件、返してほしい証拠を含めます。単に「調べる」ではなく、確認するコード、実行する検証、報告形式まで指定すると、親タスクが結果を比較できます。

## 親タスクと子タスク

親子関係はタスク一覧と子タスクパネルで階層を示します。子タスクの完了だけで結果が親へ自動送信されたり、親が自動再開したりすることはありません。子タスクへ移動して会話と成果を確認し、必要な結果を明示的に取得します。

子タスクの専用報告は、通常の利用者メッセージや別タスクからのメッセージと区別して表示されます。報告には状態要約と技術情報が含まれる場合があります。表示上の完了だけでなく、差分、テスト、生成物など依頼した証拠を確認してください。

## task runとreportを使う

AIエージェントから別タスクを起動して最初の報告まで待つ場合は`cockpit task run`を使います。返される`report`には状態、待機理由、増加する`seq`、最新メッセージが含まれます。

継続して次の報告を待つ場合は、処理済みの`seq`を`cockpit task wait <id> --since <seq>`へ渡します。追加指示を送り、そのターンの報告を待つ場合は`cockpit task send <id> --text "..." --wait`を使います。タイムアウトはタスク失敗を意味しません。現在状態を確認してから同じ`seq`で待機を続けます。

親子関係だけを報告配送の代わりにしないことが重要です。正確な結果契約は[`cockpit task` Reference](https://agi-labo.com/tools/cockpit/docs/cockpit-cli/reference/task)を参照してください。

## Master Agentを使う

Master Agentは、複数プロジェクトを横断してタスクを分解し、Cockpitのタスク制御を使って進捗を監督するための専用タスクです。通常タスクと同じく、作業範囲と外部操作の権限は利用者の指示に従います。

Master Agentへは、最終目的、優先順位、対象リポジトリ、避ける操作、完了条件を渡します。途中の報告は完了の証明ではありません。すべての子タスク結果を回収し、依存関係と未解決事項を照合してから最終判断します。

## テンプレートを使う

公式テンプレートと利用者テンプレートは、繰り返すタスク指示と入力変数を保存します。テンプレートは実行時のエージェント、作業場所、権限を固定するものではありません。作成画面で展開後の指示と実行設定を確認してください。

CLIでは`cockpit templates`で一覧、取得、作成、更新、削除を行います。テンプレートの詳細は[`cockpit settings` Reference](https://agi-labo.com/tools/cockpit/docs/cockpit-cli/reference/settings)に含まれます。

## Fleetで依存関係を宣言する

Fleetは、複数タスクをノードとする依存関係付きのRunをYAMLで宣言し、並列実行、gate、再試行、再開を管理します。単純な子タスク一件には使わず、依存順、並列性、承認点、再実行条件を繰り返し使う処理に向いています。

Runにはタイトルを付けられ、Fleetグループには各ノードの進捗が表示されます。ノード単位の通知は抑えられ、Runの終了時にまとめて通知されます。command gateの一時的な失敗は設定された範囲で再試行されます。手動でPull Requestをマージした場合も、保存済みRunから復旧できます。

FleetのYAML、gate、操作、復旧は[`cockpit fleet` Reference](https://agi-labo.com/tools/cockpit/docs/cockpit-cli/reference/fleet)を参照してください。

## Talk Roomsで会話する

Talk Roomは、複数のエージェントと人が一つの共有会話で議論する面です。命令を一方向に渡す`task send`とは異なり、roomの履歴が会話の正本になります。

作成者はすべての新着で再開し、後から参加したエージェントは既定で自分への`@mention`だけで再開します。全員がすべての発言へ反応する議論では、参加時に`notify all`を指定します。不要な再開を防ぐ場合は`mentions`または`none`を使い、必要な相手を表示名で明示的に呼びます。

閉じたRoomは履歴を保持しますが、再度開くまで発言できません。参加、通知、待機、再開の契約は[`cockpit talk` Reference](https://agi-labo.com/tools/cockpit/docs/cockpit-cli/reference/talk)を参照してください。

## AskとAutorunを分離する

[Ask](https://agi-labo.com/tools/cockpit/docs/ask)は人の判断を受け取り、元のタスクを再開します。[Autorun](https://agi-labo.com/tools/cockpit/docs/autorun)は時刻、間隔、cronで新しいタスクを起動するか既存タスクへ指示を送ります。どちらも、複数タスクの結果を自動的に統合する仕組みではありません。

判断が必要ならAsk、開始時刻を管理するならAutorun、依存する複数タスクを進めるなら親子タスクまたはFleet、複数参加者が議論するならTalk Roomを選びます。

## 完了を判定する

オーケストレーション全体の完了には、次の証拠が必要です。

- 必要な子タスクまたはFleetノードが終端状態になっている
- 各結果を親側で取得し、依存関係どおりに統合している
- 失敗、スキップ、タイムアウト、未回答のAskが残っていない
- 差分、テスト、公開状態など最終目的の受け入れ条件を確認している
- 不要になったRoom、Run、タスクを削除する前に必要な履歴を保存している

成果の確認面は[成果とツール](https://agi-labo.com/tools/cockpit/docs/results-and-tools)、権限と削除境界は[セキュリティとデータ](https://agi-labo.com/tools/cockpit/docs/security-and-data)を参照してください。
