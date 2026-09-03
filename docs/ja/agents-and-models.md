<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# エージェントとモデル

8種類のエージェント、ネイティブUIとターミナル、モデル、推論レベル、アカウント、承認、再開、使用量の違いを説明します。

> AGI Cockpit 4.69.0で2026-09-04に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/agents-and-models)

AGI Cockpitでは、同じタスク作成面から8種類のエージェントを選べます。ただし、UIモード、モデル、推論レベル、アカウント、承認、再開などの対応は同一ではありません。表示された設定だけが、そのエージェントと実行面で利用できる現在の選択肢です。

## エージェントを選ぶ

| エージェント | 主な選択基準 |
| --- | --- |
| Claude Code | Claudeのネイティブ会話、ターミナル、システムプロンプト、外部セッション取り込みを使う |
| Codex | Codexのネイティブ会話、ターミナル、service tier、Goal、外部セッション取り込みを使う |
| Antigravity | Antigravityのモデルと複数プロファイルを使う |
| Cursor | Cursorのネイティブ会話とターミナル、動的に取得したモデルを使う |
| Qoder | Qoderのネイティブ会話とターミナル、システムプロンプト、turn数付きGoalを使う |
| Grok Build | Grok Buildのネイティブ会話とターミナル、進行中ワークフローを再開する |
| Terminal | 任意のシェルコマンドをターミナルとして実行する |
| Cockpit | OpenRouter、OpenCode Go、OpenCode Zen、LM Studioの対応モデルをCockpit内のネイティブUIで使う |

エージェントCLIを必要とする種類は、CockpitがCLIを検出できる場合だけ作成画面へ表示されます。CockpitとTerminalは外部エージェントCLIの検出を必要としません。

## ネイティブUIとターミナル

ネイティブUIは、Cockpitが会話、ツール実行、使用量、モデル、承認状態を構造化して表示するモードです。ターミナルは、選択したCLIをPTY内で直接操作するモードです。内部値とCLIではそれぞれ`visual`、`terminal`として表されます。

Claude Code、Codex、Antigravity、Cursor、Qoder、Grok Buildは両方のモードに対応します。Terminalはターミナルだけ、CockpitはネイティブUIだけです。設定変更後に作成済みタスクのモードが自動で変わることはありません。

AntigravityのネイティブUIでは、失敗したツール項目を失敗として残しながら、その後にエージェントが応答を続けたターンは完了できます。エージェントがバックグラウンドコマンドの完了を待つ中間回答を返した場合は、CLIの成功通知だけでターンを閉じず、完了通知、その後のツール実行、最終回答まで同じターンとして追跡します。常駐サーバーなどを途中で確認した後にエージェントが最終回答を返した場合は、プロセスを残したままターンを完了できます。

## モデルと推論設定

モデル、推論レベル、service tier、システムプロンプトは、能力レジストリと実行時の候補取得が対応する範囲で表示されます。ネイティブUIの会話がまだ空でも、モデル選択にはそのタスクのエージェントに対応する候補が表示されます。実行時の候補が遅れて届いた場合も、その間に利用者が選んだ有効なモデルを既定値へ戻しません。候補を検証できない設定は、CLIとAPIが無言で別の値へ置き換えず拒否します。

ClaudeのネイティブUIは、実行時の候補取得で返されないモデルも組み込み候補で補完します。組み込み候補には**Claude Fable 5.1**が含まれ、推論レベルは`low`、`medium`、`high`、`xhigh`、`max`、既定は`high`です。候補への追加だけで、作成済みタスクの選択モデルは変更しません。

Codexのモデル選択肢は、組み込み候補と実行時に取得した候補のどちらも、新しいGPT世代とバージョンを先にし、同じバージョンでは標準モデルから用途別variantの順に並びます。モデル名をアルファベット順に探すのではなく、上から性能と用途を比較できます。現在選択中の有効なモデルを、並べ替えだけで変更することはありません。

service tierはCodexの対応モデルだけで`standard`または`fast`を選べます。システムプロンプトはClaude、Codex、Qoder、CockpitのネイティブUIで利用できます。`append`はCockpit標準の指示を維持し、`replace`は標準指示を置き換えるため、Cockpit CLIの知識はインストール済みskillからだけ利用できる状態になります。

Cockpit AgentのモデルIDは、OpenRouterが`openrouter/<id>`、OpenCode Goが`opencode-go/<id>`、OpenCode Zenが`opencode/<id>`、LM Studioが`lmstudio/<id>`です。OpenCode GoとOpenCode Zenは別のプロバイダーで、設定の**OpenCode Go API Key**と**OpenCode Zen API Key**も共有しません。選択したプロバイダーのkeyがない場合、そのモデル一覧とタスクは利用できません。

カスタムシステムプロンプトは`cockpit system-prompt add`で登録すると、DesktopとPWAの新しいタスク画面とAutorunの選択肢に表示されます。

```bash
cockpit system-prompt add reviewer --prompt "変更の正しさと分かりやすさをレビューしてください。"
cockpit system-prompt list
```

カスタムプロンプトは利用者所有のMarkdownとしてAGI Toolsのデータ領域に保存されます。内容は選択したエージェントへ送られるため、認証情報や秘密を含めないでください。Cursor、Grok Build、Antigravity、Terminal、ターミナルUIでは選択できません。

## アカウントとAuto

Claude、Codex、Antigravity、Cursor、Qoder、Grok Buildは、デフォルトアカウントと名前付きプロファイルを利用できます。新しいタスク、Autorun、Fleetの既定はAutoです。Autoは短い利用枠を可用性の判定に使い、利用可能なアカウントは最も遅くリセットされる長期枠の残量と残り時間から優先します。利用上限またはプラン制限に達した場合は、利用可能な別アカウントへ切り替えて保存済みセッションを再開します。

タスク作成時にAutoまたは固定アカウントを選ぶと、その選択と現在の実行先がタスクへ保存され、画面表示と次のランタイムに同じ状態が使われます。固定アカウントは自動切り替えを行いません。実行中タスクで切り替える場合は現在のランタイムを停止し、保存済み会話を移して選択したプロファイルで再開します。Claudeの利用クレジット枯渇とCodexのワークスペースクレジット枯渇も利用上限として扱われます。

期限切れの認証情報は「セッション期限切れ」と表示され、`authState: expired`としてAutoとFleet開始前の確認から除外されます。再ログインすると認証判定と利用状況が再確認されます。

Antigravityの名前付きプロファイルはブラウザーでGoogle OAuthを行い、会話、ログ、キャッシュ、利用履歴を専用homeへ分離します。macOSではプロファイル専用Keychainを認証とクォータの境界に使い、検索順を確認できなければタスクを開始しません。WindowsとLinuxのkeyringはOS全体で共有されるため、host loginが残っている場合はprofile tokenより優先されます。開発用シェル資源と認証情報を含まない設定は通常のhomeと共有します。取得元の確認と安全なlogout手順は[アカウントとAuto](https://agi-labo.com/tools/cockpit/docs/accounts)を参照してください。

## 承認モード

`supervised`は必要なツール操作ごとに承認を求め、`accept-edits`は対応する編集操作を許可し、`full-access`はより広い操作を許可します。対応範囲はエージェントとUIモードにより異なります。

AntigravityのネイティブUIは実行中に承認を質問できないため、`supervised`では承認が必要なツールを自動拒否します。編集やコマンドが必要なタスクでは、目的とリスクを確認したうえで`accept-edits`または`full-access`を選びます。Askへの回答は承認モードの変更ではありません。

## 再開、使用量、Goal

ネイティブUIの対応エージェントは保存済みセッションを再開できます。Terminalタスクは以前のシェルプロセスを復元できず、再開時に同じディレクトリで新しいシェルを起動します。Cursor、Qoder、Grok Buildでは接続先の保存済み会話も復元され、Grok Buildの進行中ワークフローは進行中として戻ります。AntigravityのネイティブUIでtimerなどにより予定された継続処理が再起動をまたぐ場合は、保存済みtranscriptから元のturnへ同期し、別の新規turnとして重複表示しません。

プロバイダーの利用枠と上限は、ランタイムから現在値を取得できる場合だけ表示されます。認証が必要、取得失敗、更新時刻が古い状態を残量ゼロと同一視しないでください。

コンテキスト使用量は別の表示契約です。CursorのネイティブUIでランタイムがトークン使用量を返さない場合、Cockpitはローカルに保持した会話から現在のコンテキストを推定し、値の先頭に`~`を付けます。上限はランタイムの値を優先し、取得できない場合は選択中のCursorモデルについて保守しているメタデータを使います。どちらにもコンテキスト長がない場合は上限を「利用不可」と表示し、割合を計算しません。保持履歴の推定量がモデルの上限を超える場合は、表示する有効コンテキストの推定値を上限までに抑え、メーターを中立表示にします。ランタイム側の圧縮や履歴切り捨てにより、実際の有効コンテキストが減っている可能性があるためです。これらの推定値はプロバイダーの利用枠や請求値ではありません。

対応エージェントでは`/goal`から目的を設定します。Codexはtoken予算、Qoderはturn数を設定でき、Claude、Codex、Qoder、Grok Buildでは保存済み状態を表示できます。CodexのネイティブUIで送った`/goal`は、Goal状態と利用者メッセージを保存するだけでなく、その目的を進める実際のturnを開始します。AntigravityとCursorは実行時に設定操作を利用できますが、永続状態表示の契約はありません。

## 添付、skill、外部セッション

すべてのエージェントをDesktop、PWA、CLIから作成でき、添付の入口を持ちます。ただし、画像、PDF、Office文書などをネイティブに解釈するか、ローカルパスとして渡すかはエージェント、UIモード、モデルで異なります。

AntigravityのネイティブUIは、DesktopとPWAからの画像添付をネイティブに受け取ります。画像がタスクの作業場所外にある場合、CockpitはGitから除外した一時ディレクトリとして作業場所内へコピーし、そのパスをAntigravityへ渡します。このため`supervised`でも画像を読み取れますが、作業場所へ一時ファイルを書き込めない場合はターンを開始せず理由を表示します。PDFなど画像以外と、AntigravityのターミナルUIはこのネイティブ画像入力の対象外です。

Cockpit skillとHTML Modeは外部エージェントCLIへインストールされます。TerminalとCockpit Agentにはこのskill契約を適用しません。Cockpit内の履歴から外部セッションとして取り込めるのはClaude CodeとCodexです。

## 現在の対応機能比較

以下の表は、Desktop、PWA、CLIが共用する型付き能力レジストリから生成されます。モデルや推論レベルは、対応するネイティブUIで利用可能な候補を取得できる場合に選択できます。表が本文の説明と異なる場合は、生成元の能力レジストリと現在の作成画面を優先してください。

タスクの作成手順は[最初のタスク](https://agi-labo.com/tools/cockpit/docs/first-task)、設定と復旧は[Referenceとサポート](https://agi-labo.com/tools/cockpit/docs/reference-and-support)、データ境界は[セキュリティとデータ](https://agi-labo.com/tools/cockpit/docs/security-and-data)を参照してください。
