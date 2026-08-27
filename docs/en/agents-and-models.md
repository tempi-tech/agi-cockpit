<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Agents and models

Compare eight agents, native and terminal UI, models, reasoning levels, accounts, approvals, resume behavior, and usage reporting.

> Verified with AGI Cockpit 4.61.0 on 2026-08-27. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/agents-and-models)

AGI Cockpit lets you choose from eight agents on the same task creation surface. Their support for UI modes, models, reasoning levels, accounts, approvals, and resume behavior is not identical. Only settings displayed for the selected agent and execution mode are currently available.

## Choose an agent

| Agent | Choose it when you need |
| --- | --- |
| Claude Code | Claude native conversation, terminal mode, system prompts, and external session import |
| Codex | Codex native conversation, terminal mode, service tier, Goals, and external session import |
| Antigravity | Antigravity models and multiple profiles |
| Cursor | Cursor native conversation and terminal mode with dynamically retrieved models |
| Qoder | Qoder native conversation and terminal mode, system prompts, and turn-limited Goals |
| Grok Build | Grok Build native conversation and terminal mode with resume of active workflows |
| Terminal | An arbitrary shell command in a terminal |
| Cockpit | Supported OpenRouter, OpenCode Go, and LM Studio models in Cockpit's native UI |

Agent types that depend on an external CLI appear on the creation screen only when Cockpit can detect that CLI. Cockpit and Terminal do not require external agent CLI detection.

## Native UI and terminal UI

Native UI lets Cockpit display conversation, tool execution, usage, model, and approval state as structured data. Terminal UI operates the selected CLI directly in a PTY. Their internal and CLI values are `visual` and `terminal`.

Claude Code, Codex, Antigravity, Cursor, Qoder, and Grok Build support both modes. Terminal supports terminal mode only, and Cockpit supports native UI only. Changing defaults does not migrate the mode of an existing task.

## Models and reasoning settings

Models, reasoning levels, service tiers, and system prompts are displayed only when both the capability registry and runtime option discovery support them. If options cannot be retrieved, the UI and CLI reject an unverified choice instead of silently replacing it with a default.

Codex supports a `standard` or `fast` service tier for applicable models. System prompts are available in native UI for Claude, Codex, Qoder, and Cockpit. `append` preserves Cockpit's standard instructions. `replace` replaces them, leaving Cockpit CLI knowledge available only through an installed skill.

## Accounts and Auto

Claude, Codex, Antigravity, Cursor, Qoder, and Grok Build support a default account and named profiles. Auto is the default for new tasks, Autoruns, and Fleet nodes. It inspects signed-in account usage, chooses a runtime, and can switch to another available account and resume the saved session after a usage or plan limit.

A fixed account does not switch automatically. Switching an active task stops its current runtime and resumes the saved conversation with the selected profile. Exhausted Codex workspace credits are treated as a usage limit.

Antigravity named profiles keep OAuth tokens, conversations, logs, and usage history in profile-specific storage. They do not fall back to a shared keyring.

## Approval modes

`supervised` asks for approval when a tool action requires it, `accept-edits` permits supported editing operations, and `full-access` permits a broader range of actions. Exact support depends on the agent and UI mode.

Antigravity native UI cannot ask an approval question while it runs, so `supervised` automatically rejects tools that require approval. Choose `accept-edits` or `full-access` for work that needs editing or commands only after checking the objective and risk. An Ask answer does not change the approval mode.

## Resume, usage, and Goals

Supported native UI agents can resume a saved session. A Terminal task cannot restore its former shell process and instead opens a new shell in the same directory. Cursor, Qoder, and Grok Build also restore the connected provider's saved conversation, and an in-progress Grok Build workflow returns as in progress.

Usage and limits appear only for agents whose runtime reports current values. Do not treat an authentication requirement, retrieval failure, or stale update as zero remaining usage.

Supported agents use `/goal` to set an objective. Codex can apply a token budget, Qoder can apply a turn limit, and Claude, Codex, Qoder, and Grok Build expose persisted goal state. Antigravity and Cursor provide a runtime goal-setting operation without a persisted-state display contract.

## Attachments, skills, and external sessions

Every agent has an attachment entry point when created from Desktop, the PWA, or CLI. Whether an image, PDF, Office document, or another file is interpreted natively or passed as a local path depends on the agent, UI mode, and model.

The Cockpit skill and HTML Mode are installed into supported external agent CLIs. Terminal and Cockpit Agent do not use that skill contract. Claude Code and Codex are the only agents whose external sessions can be imported from Cockpit's history.

## Current capability comparison

The tables below are generated from the same typed capability registry used by task creation and runtime validation. They therefore show current support without maintaining a separate handwritten matrix.

See [Install and run your first task](https://agi-labo.com/en/tools/cockpit/docs/getting-started) for task creation, [Reference and support](https://agi-labo.com/en/tools/cockpit/docs/reference-and-support) for configuration and recovery, and [Security and data](https://agi-labo.com/en/tools/cockpit/docs/security-and-data) for data boundaries.
