<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# システムプロンプト

標準指示とカスタム指示の合成方法、appendとreplaceの違い、登録、選択、保存、安全な運用を説明します。

> AGI Cockpit 4.63.0で2026-08-29に確認済み。 [公式ドキュメントを表示](https://agi-labo.com/tools/cockpit/docs/system-prompts)

システムプロンプトは、個別タスクの依頼より前に、エージェントの役割、判断基準、出力方針、作業規則を与える指示です。AGI Cockpitの標準プロンプトに加えて、用途別のカスタムプロンプトを登録し、新しいタスクやAutorunで選択できます。

## タスクの依頼と区別する

タスクの入力欄には、その仕事で達成したい具体的な目的を書きます。システムプロンプトには、複数の仕事で繰り返し使う役割や品質基準を書きます。依頼ごとに変わる対象ファイル、期限、公開先、承認はシステムプロンプトへ固定せず、タスクの指示として渡してください。

システムプロンプトは権限を増やしません。ファイル編集、コマンド、外部送信、公開などは、選択したエージェント、承認モード、通常の安全境界に従います。

## appendとreplaceを選ぶ

| モード | 合成結果 | 適する用途 |
| --- | --- | --- |
| `append` | Cockpitの標準プロンプトの後へカスタム指示を追加する | レビュー基準、文章のトーン、専門的な役割を追加する |
| `replace` | Cockpitの標準プロンプトを使わず、カスタム指示だけを渡す | 全体の指示構成を自分で管理する必要がある高度な用途 |

既定は`append`です。`replace`ではCockpit CLIの知識が標準プロンプトから外れ、インストール済みのCockpit skillからだけ利用できる状態になります。迷う場合は`append`を使います。

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
