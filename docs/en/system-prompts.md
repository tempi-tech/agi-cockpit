<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# System prompts

Understand the built-in Cockpit, Claude Code, Cowork, and Codex presets, plus custom prompt append and replace modes, selection, and safe operation.

> Verified with AGI Cockpit 4.63.0 on 2026-08-29. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/system-prompts)

A system prompt gives an agent its role, decision criteria, output policy, and working rules before the task-specific instruction. AGI Cockpit lets you register reusable custom prompts and select them for new tasks and Autoruns alongside Cockpit's standard prompt.

## Separate the system prompt from the task

Put the concrete outcome for one job in the task instruction. Put roles and quality standards that should repeat across jobs in a system prompt. File targets, deadlines, publication destinations, and approvals that vary per job should remain in the task instruction instead of being fixed globally.

A system prompt does not grant permissions. File edits, commands, external transfers, and publishing remain governed by the selected agent, approval mode, and normal safety boundaries.

## Understand the built-in presets

AGI Cockpit includes system-prompt presets that are available immediately for each supported agent. **Cockpit** is the default for every supported agent. Built-in presets are complete alternatives and cannot be stacked. `cockpit system-prompt list` reports them with `source: default` and `mode: replace`; they cannot be edited or removed.

| Agent | Preset | Instructions and intended use |
| --- | --- | --- |
| Claude, Codex, Qoder, Cockpit | **Cockpit** (default) | Uses Cockpit's standard prompt covering the task environment, CLI, Ask, supervision, verification, approval boundaries, and short, concrete reporting |
| Claude | **Claude Code** | Uses the Claude Agent SDK's built-in `claude_code` preset and appends a short Cockpit environment and CLI brief. Choose it when Claude Code's working conventions should take priority |
| Claude | **Cowork** | Uses Cockpit's bundled Cowork prompt for general knowledge work, interaction, safety, writing tone, task tracking, and citations, followed by a short Cockpit brief |
| Codex | **Codex (Friendly)** | Selects the Codex runtime's `friendly` personality with a short Cockpit environment and CLI brief. It keeps Codex's standard behavior while requesting a warmer response style |
| Codex | **Codex (Pragmatic)** | Selects the Codex runtime's `pragmatic` personality with a short Cockpit environment and CLI brief. It keeps Codex's standard behavior while requesting a practical, direct response style |
| Qoder | **Qoder** | Does not supply Cockpit's custom system prompt and uses Qoder's standard prompt |

Use **Cockpit** as the baseline when the agent should consistently work as a task delegated and supervised from Cockpit and understand Ask, side panels, and child tasks. Claude's **Claude Code** and **Cowork** presets and the two Codex presets prioritize the provider's working style while retaining enough context to operate inside Cockpit. Exact Codex personality wording can change with the Codex runtime or model.

Selecting **Claude Code** still runs the task in Cockpit's native UI through the Claude Agent SDK; it does not switch to Claude Code's terminal UI. Conversely, Claude and Codex terminal UI tasks do not use this selector. Their CLI's own system prompt and configuration apply instead.

Change the per-agent default under Agents in Settings. These CLI settings become the default for tasks created afterward:

```bash
cockpit settings set agents.systemPromptPreset.claude claude-code
cockpit settings set agents.systemPromptPreset.codex codex-friendly
cockpit settings reset agents.systemPromptPreset.claude
```

`reset` returns the agent to the default **Cockpit** preset. A settings change does not rewrite existing tasks or a saved Autorun runtime snapshot. To override one task only, pass `--system-prompt claude-code` or `--system-prompt codex-pragmatic` when creating it.

## Choose append or replace

| Mode | Composition | Appropriate use |
| --- | --- | --- |
| `append` | Adds the custom instructions after Cockpit's standard prompt | Add review criteria, writing tone, or a specialist role |
| `replace` | Sends only the custom instructions and omits Cockpit's standard prompt | Advanced cases where you maintain the entire instruction structure |

`append` and `replace` control how a user-created custom prompt is composed. The default is `append`. An appended custom prompt always follows the **Cockpit standard prompt**; it is not appended after another built-in preset such as Claude Code, Cowork, Codex (Friendly), or Codex (Pragmatic).

With `replace`, Cockpit CLI knowledge is no longer supplied by the standard prompt and is available only through the installed Cockpit skill. Use `append` unless full replacement is intentional.

## Register a custom prompt

Use `--prompt` for a short value, `--file` for an existing Markdown file, or `--stdin` for multiline and quoted content.

```bash
cockpit system-prompt add reviewer --prompt "Review correctness, maintainability, and user impact."
cockpit system-prompt add persona --mode replace --file ./persona.md
cat research.md | cockpit system-prompt add researcher --stdin
cockpit system-prompt list
```

The name appears as a selectable option. An existing custom or built-in name cannot be overwritten. Custom prompts are stored as user-owned Markdown in the AGI Tools data area and are not overwritten when Cockpit redeploys its managed files.

## Use a prompt for a task or Autorun

On Desktop or the PWA, the system-prompt selector appears after choosing a supported agent. Use `--system-prompt` from the CLI.

```bash
cockpit task create --instruction "Review this change" --directory /path/to/repo --agent-type codex --system-prompt reviewer
cockpit autorun create --name "Daily review" --instruction "Review recent changes" --directory /path/to/repo --agent-type codex --ui-mode visual --system-prompt reviewer --type cron --expression "0 9 * * *"
```

New tasks support system prompts in the native UI for Claude, Codex, Qoder, and Cockpit. Cursor, Grok Build, Antigravity, Terminal, and terminal UI modes do not support selection. If the control is not shown, that agent and execution surface do not support it.

An Autorun stores the selected prompt name in its runtime snapshot. If that prompt is removed or no longer supports the agent, a resumed task reports that it returned to the standard prompt, and an Autorun may require its settings to be reviewed.

## Design prompt content safely

State the purpose, priorities, required success conditions, prohibited actions, and expected report format briefly and clearly. Conflicting instructions and long procedures that do not apply to every task can weaken the task-specific intent.

The selected AI agent receives the prompt content. Do not put API keys, passwords, access tokens, personal data, or other secrets directly in a prompt. Use managed credentials or the target tool's authentication flow when a connection needs a secret.

## Remove a custom prompt

```bash
cockpit system-prompt remove reviewer
```

Only a custom prompt with the exact name can be removed. Built-in prompts cannot be removed. Before removal, check whether new tasks, Autoruns, or resumed tasks still depend on it.

See [Agents and models](https://agi-labo.com/en/tools/cockpit/docs/agents-and-models) for the full capability comparison, the [`cockpit system-prompt` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/system-prompt) for the command contract, and [Security and data](https://agi-labo.com/en/tools/cockpit/docs/security-and-data) for data boundaries.
