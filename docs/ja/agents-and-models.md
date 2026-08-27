<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# エージェントとモデル

8種類のエージェント、ネイティブUIとターミナル、モデル、推論レベル、アカウント、承認、再開、使用量の違いを説明します。

> AGI Cockpit 4.61.0で2026-08-27に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/agents-and-models)

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
| Cockpit | OpenRouter、OpenCode Go、LM Studioの対応モデルをCockpit内のネイティブUIで使う |

エージェントCLIを必要とする種類は、CockpitがCLIを検出できる場合だけ作成画面へ表示されます。CockpitとTerminalは外部エージェントCLIの検出を必要としません。

## ネイティブUIとターミナル

ネイティブUIは、Cockpitが会話、ツール実行、使用量、モデル、承認状態を構造化して表示するモードです。ターミナルは、選択したCLIをPTY内で直接操作するモードです。内部値とCLIではそれぞれ`visual`、`terminal`として表されます。

Claude Code、Codex、Antigravity、Cursor、Qoder、Grok Buildは両方のモードに対応します。Terminalはターミナルだけ、CockpitはネイティブUIだけです。設定変更後に作成済みタスクのモードが自動で変わることはありません。

## モデルと推論設定

モデル、推論レベル、service tier、システムプロンプトは、能力レジストリと実行時の候補取得の両方が対応する場合だけ表示されます。候補を取得できない場合、CLIとAPIは無言で既定値へ置き換えず、検証できない設定を拒否します。

service tierはCodexの対応モデルだけで`standard`または`fast`を選べます。システムプロンプトはClaude、Codex、Qoder、CockpitのネイティブUIで利用できます。`append`はCockpit標準の指示を維持し、`replace`は標準指示を置き換えるため、Cockpit CLIの知識はインストール済みskillからだけ利用できる状態になります。

## アカウントとAuto

Claude、Codex、Antigravity、Cursor、Qoder、Grok Buildは、デフォルトアカウントと名前付きプロファイルを利用できます。新しいタスク、Autorun、Fleetの既定はAutoです。Autoはログイン済みアカウントの利用状況を確認して実行先を選び、利用上限またはプラン制限に達した場合は、利用可能な別アカウントへ切り替えて保存済みセッションを再開します。

固定アカウントを選ぶと自動切り替えは行いません。実行中タスクで切り替える場合は現在のランタイムを停止し、保存済み会話を移して選択したプロファイルで再開します。Codexのワークスペースクレジット枯渇も利用上限として扱われます。

Antigravityの名前付きプロファイルは、OAuthトークン、会話、ログ、利用履歴を専用のアプリデータ領域へ分離します。共有キーリングへのフォールバックは行いません。

## 承認モード

`supervised`は必要なツール操作ごとに承認を求め、`accept-edits`は対応する編集操作を許可し、`full-access`はより広い操作を許可します。対応範囲はエージェントとUIモードにより異なります。

AntigravityのネイティブUIは実行中に承認を質問できないため、`supervised`では承認が必要なツールを自動拒否します。編集やコマンドが必要なタスクでは、目的とリスクを確認したうえで`accept-edits`または`full-access`を選びます。Askへの回答は承認モードの変更ではありません。

## 再開、使用量、Goal

ネイティブUIの対応エージェントは保存済みセッションを再開できます。Terminalタスクは以前のシェルプロセスを復元できず、再開時に同じディレクトリで新しいシェルを起動します。Cursor、Qoder、Grok Buildでは接続先の保存済み会話も復元され、Grok Buildの進行中ワークフローは進行中として戻ります。

使用量はプロバイダーから現在値を取得できるエージェントだけに表示されます。認証が必要、取得失敗、更新時刻が古い状態を残量ゼロと同一視しないでください。

対応エージェントでは`/goal`から目的を設定します。Codexはtoken予算、Qoderはturn数を設定でき、Claude、Codex、Qoder、Grok Buildでは保存済み状態を表示できます。AntigravityとCursorは実行時に設定操作を利用できますが、永続状態表示の契約はありません。

## 添付、skill、外部セッション

すべてのエージェントをDesktop、PWA、CLIから作成でき、添付の入口を持ちます。ただし、画像、PDF、Office文書などをネイティブに解釈するか、ローカルパスとして渡すかはエージェント、UIモード、モデルで異なります。

Cockpit skillとHTML Modeは外部エージェントCLIへインストールされます。TerminalとCockpit Agentにはこのskill契約を適用しません。Cockpit内の履歴から外部セッションとして取り込めるのはClaude CodeとCodexです。

## 現在の対応機能比較

以下の表は、Desktop、PWA、CLIが共用する型付き能力レジストリから生成されます。モデルや推論レベルは、対応するネイティブUIで利用可能な候補を取得できる場合に選択できます。表が本文の説明と異なる場合は、生成元の能力レジストリと現在の作成画面を優先してください。

タスクの作成手順は[インストールと最初のタスク](https://agi-labo.com/tools/cockpit/docs/getting-started)、設定と復旧は[Referenceとサポート](https://agi-labo.com/tools/cockpit/docs/reference-and-support)、データ境界は[セキュリティとデータ](https://agi-labo.com/tools/cockpit/docs/security-and-data)を参照してください。
