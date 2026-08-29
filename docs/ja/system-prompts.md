<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# システムプロンプト

Cockpit、Claude Code、Cowork、Codexなどの組み込みプリセットと、カスタム指示のappend・replace、登録、選択、安全な運用を説明します。

> AGI Cockpit 4.63.0で2026-08-29に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/system-prompts)

システムプロンプトは、個別タスクの依頼より前に、エージェントの役割、判断基準、出力方針、作業規則を与える指示です。AGI Cockpitの標準プロンプトに加えて、用途別のカスタムプロンプトを登録し、新しいタスクやAutorunで選択できます。

## タスクの依頼と区別する

タスクの入力欄には、その仕事で達成したい具体的な目的を書きます。システムプロンプトには、複数の仕事で繰り返し使う役割や品質基準を書きます。依頼ごとに変わる対象ファイル、期限、公開先、承認はシステムプロンプトへ固定せず、タスクの指示として渡してください。

システムプロンプトは権限を増やしません。ファイル編集、コマンド、外部送信、公開などは、選択したエージェント、承認モード、通常の安全境界に従います。

## 組み込みプリセットを理解する

AGI Cockpitには、エージェントごとに最初から選べるシステムプロンプトプリセットがあります。すべての対応エージェントで既定は**Cockpit**です。組み込みプリセットは完成済みの選択肢で、互いに重ねて使いません。`cockpit system-prompt list`では`source: default`、`mode: replace`として表示され、編集や削除はできません。

| エージェント | プリセット | 渡される指示と用途 |
| --- | --- | --- |
| Claude、Codex、Qoder、Cockpit | **Cockpit**（既定） | Cockpitのタスク環境、CLI、Ask、監督、検証、承認境界、短く具体的な報告などを含むCockpit向け標準プロンプトを使う |
| Claude | **Claude Code** | Claude Agent SDKの`claude_code`組み込みプリセットへ、Cockpitの環境とCLIの短い案内を追加する。Claude Codeに近い作業規則を優先したい場合に使う |
| Claude | **Cowork** | 一般的な知識作業、対話、安全性、文章のトーン、タスク管理、出典の扱いを含むCockpit同梱のCowork向けプロンプトへ、Cockpitの短い案内を追加する |
| Codex | **Codex (Friendly)** | Codexランタイムの`friendly` personalityと、Cockpitの環境・CLIの短い案内を使う。Codex標準の振る舞いを保ちながら、親しみのある応答傾向を選ぶ |
| Codex | **Codex (Pragmatic)** | Codexランタイムの`pragmatic` personalityと、Cockpitの環境・CLIの短い案内を使う。Codex標準の振る舞いを保ちながら、実務的で直接的な応答傾向を選ぶ |
| Qoder | **Qoder** | Cockpit独自のシステムプロンプトを渡さず、Qoder側の標準プロンプトを使う |

**Cockpit**は、エージェントをCockpitから委任・監督し、Ask、サイドパネル、子タスクなどを一貫して使わせたい場合の基準です。Claudeの**Claude Code**と**Cowork**、Codexの2種類は、プロバイダー側の作業スタイルを優先しつつ、Cockpit内で動いていることを理解させる構成です。Codex personalityの細かな表現は、使用するCodexランタイムやモデルの更新で変わる場合があります。

Claudeの**Claude Code**を選んでも、タスクの実行面はCockpitのネイティブUIとClaude Agent SDKです。Claude CodeのターミナルUIへ切り替わるわけではありません。反対に、ClaudeやCodexのターミナルUIではこの選択欄を使わず、各CLI自身のシステムプロンプトと設定が適用されます。

SettingsのAgentsでエージェントごとの既定を変更できます。CLIでは次の設定が、以後に作成するタスクの既定になります。

```bash
cockpit settings set agents.systemPromptPreset.claude claude-code
cockpit settings set agents.systemPromptPreset.codex codex-friendly
cockpit settings reset agents.systemPromptPreset.claude
```

`reset`すると既定の**Cockpit**へ戻ります。設定変更は既存タスクや保存済みAutorunのランタイム設定を書き換えません。個別タスクだけ変更する場合は、作成時に`--system-prompt claude-code`や`--system-prompt codex-pragmatic`を指定します。

## appendとreplaceを選ぶ

| モード | 合成結果 | 適する用途 |
| --- | --- | --- |
| `append` | Cockpitの標準プロンプトの後へカスタム指示を追加する | レビュー基準、文章のトーン、専門的な役割を追加する |
| `replace` | Cockpitの標準プロンプトを使わず、カスタム指示だけを渡す | 全体の指示構成を自分で管理する必要がある高度な用途 |

`append`と`replace`は、利用者が登録するカスタムプロンプトの合成方法です。既定は`append`です。`append`は常に**Cockpit標準プロンプト**へ追加され、Claude Code、Cowork、Codex (Friendly)、Codex (Pragmatic)など、別の組み込みプリセットの後へ追加されるわけではありません。

`replace`ではCockpit CLIの知識が標準プロンプトから外れ、インストール済みのCockpit skillからだけ利用できる状態になります。迷う場合は`append`を使います。

## カスタムプロンプトを登録する

短い内容は`--prompt`、既存のMarkdownは`--file`、複数行や引用を含む内容は`--stdin`で登録します。

```bash
cockpit system-prompt add reviewer --prompt "変更の正しさ、保守性、利用者への影響を確認してください。"
cockpit system-prompt add persona --mode replace --file ./persona.md
cat research.md | cockpit system-prompt add researcher --stdin
cockpit system-prompt list
```

名前は選択肢として表示され、同名の既存プロンプトや組み込みプロンプトを上書きできません。内容は利用者所有のMarkdownとしてAGI Toolsのデータ領域に保存され、Cockpitの再配備では上書きされません。

## タスクとAutorunで使う

DesktopまたはPWAの新しいタスク画面で、対応エージェントを選ぶとシステムプロンプトの選択肢が表示されます。CLIでは`--system-prompt`を指定します。

```bash
cockpit task create --instruction "この変更をレビューして" --directory /path/to/repo --agent-type codex --system-prompt reviewer
cockpit autorun create --name "Daily review" --instruction "最近の変更をレビューして" --directory /path/to/repo --agent-type codex --ui-mode visual --system-prompt reviewer --type cron --expression "0 9 * * *"
```

新規タスクで選べるのはClaude、Codex、Qoder、CockpitのネイティブUIです。Cursor、Grok Build、Antigravity、Terminalと、各エージェントのターミナルUIでは選択できません。画面に選択欄が表示されない場合、そのエージェントと実行面では利用できません。

Autorunは保存時のシステムプロンプト名をランタイム設定として保持します。プロンプトを削除したり対応条件が変わったりした場合、既存タスクの再開では標準プロンプトへ戻したことが表示され、Autorunは設定の再確認が必要になる場合があります。

## 内容を安全に設計する

プロンプトには、目的、優先順位、必須の成功条件、禁止事項、期待する報告形式を短く明確に書きます。互いに矛盾する指示や、どの作業にも当てはまらない長い手順を入れると、個別タスクの意図が弱くなります。

登録内容は選択したAIエージェントへ送信されます。APIキー、パスワード、アクセストークン、個人情報などの秘密を直接書かないでください。秘密が必要な連携は、管理された資格情報や対象ツールの認証機構を使います。

## 削除する

```bash
cockpit system-prompt remove reviewer
```

削除できるのは完全一致する名前のカスタムプロンプトだけです。組み込みプロンプトは削除できません。削除前に、新しいタスク、Autorun、既存タスクの再開で使われていないか確認してください。

対応エージェント全体の比較は[エージェントとモデル](https://agi-labo.com/tools/cockpit/docs/agents-and-models)、正確なコマンド契約は[`cockpit system-prompt` Reference](https://agi-labo.com/tools/cockpit/docs/cockpit-cli/reference/system-prompt)、データ境界は[セキュリティとデータ](https://agi-labo.com/tools/cockpit/docs/security-and-data)を参照してください。
