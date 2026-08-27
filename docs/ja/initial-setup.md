<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# 初回セットアップ

利用するAIエージェントを確認し、Cockpit skillとcockpitコマンドを準備して、最初のタスクを作れる状態にする手順です。

> AGI Cockpit 4.61.0で2026-08-27に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/initial-setup)

この手順を終えると、利用するエージェントとCockpit連携が準備され、最初のタスクを作成できます。初回起動時に自動で開かなかった場合は、画面左下のアプリメニューから「セットアップ」→「初回セットアップ」を開きます。

## 1. エージェントを確認する

セットアップはClaude Code、Codex CLI、Grok Build、Antigravity CLI、Cursor CLI、Qoder CLIを確認します。各CLIのインストール状態と、一部エージェントではサインイン状態も表示します。利用可能なエージェントが一つあれば続行できます。

使いたいエージェントが見つからない場合は「インストール」を選びます。Cockpit AgentとTerminalは外部CLIを必要としません。

## 2. Cockpit連携を設定する

連携設定は、検出済みの外部エージェントへCockpit skillをインストールまたは更新し、`cockpit`コマンドを利用できるようにします。macOSとLinuxではシェル設定、Windowsではユーザー`PATH`へコマンドの場所を追加します。

この処理は各エージェントのユーザー単位のskillディレクトリと、該当するユーザー単位のパス設定を更新します。HTML Modeは任意で、別にインストールできます。後からエージェントを追加した場合は、アプリメニューの「スキル」で状態を確認します。

## 3. エージェントへサインインする

AIエージェント側の契約と認証は、AGIラボへのサインインとは別です。Claude Code、Codexなどは、それぞれのアカウントで利用可能な状態にします。

Claude Code、Codex、Grok BuildのネイティブUIは、未認証でタスクを開始した場合もタスク詳細にログイン案内を表示し、認証後に最初の指示を再試行します。ターミナルUIとTerminalでは、表示されたターミナル内で対象CLIのログインを完了してからタスクを再開または作り直します。

## 4. 利用方法を選ぶ

ローカル機能だけを使う場合は、最後の画面で「サインインせずに始める」を選べます。Autorunまたは別端末からのリモートアクセスを使う場合は、AGIラボ会員としてサインインします。

「新しいタスク」画面が開き、利用するエージェントを選択できればセットアップ完了です。[最初のタスク](https://agi-labo.com/tools/cockpit/docs/first-task)へ進みます。

## セットアップを完了できない場合

| 表示または状態 | 確認すること |
| --- | --- |
| 使いたいエージェントが一覧にない | 「エージェントを管理」で対象CLIをインストールするか、設定の起動コマンドを確認する |
| 「エージェントを確認中」と表示される | 保存済み候補を表示しながら再確認しているため、完了を待つ |
| 「エージェントを確認できませんでした」と表示される | 保存済み候補を確認し、設定の「エージェント」でCLIの起動コマンドを確認する |
| ネイティブUIでログインが必要 | タスク詳細のログイン案内を完了し、同じ指示の再試行を待つ |
| ターミナルUIまたはTerminalでログインが必要 | ターミナル内で対象CLIのログインを完了し、タスクを再開または作り直す |

アカウントプロファイル、Auto、モデル、カスタムシステムプロンプトなど、初回セットアップ後の設定は[エージェントとモデル](https://agi-labo.com/tools/cockpit/docs/agents-and-models)を参照してください。

## 関連ページ

- [AGI Cockpitをインストールする](https://agi-labo.com/tools/cockpit/docs/getting-started)
- [最初のタスク](https://agi-labo.com/tools/cockpit/docs/first-task)
- [エージェントとモデル](https://agi-labo.com/tools/cockpit/docs/agents-and-models)
- [`cockpit setup` Reference](https://agi-labo.com/tools/cockpit/docs/cockpit-cli/reference/setup)
- [セキュリティとデータ](https://agi-labo.com/tools/cockpit/docs/security-and-data)
