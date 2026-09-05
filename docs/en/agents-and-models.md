<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Agents and models

Compare eight agents, native and terminal UI, models, reasoning levels, accounts, approvals, resume behavior, and usage reporting.

> Verified with AGI Cockpit 4.71.0 on 2026-09-05. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/agents-and-models)

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
| Cockpit | Supported OpenRouter, OpenCode Go, OpenCode Zen, and LM Studio models in Cockpit's native UI |

Agent types that depend on an external CLI appear on the creation screen only when Cockpit can detect that CLI. Cockpit and Terminal do not require external agent CLI detection.

## Native UI and terminal UI

Native UI lets Cockpit display conversation, tool execution, usage, model, and approval state as structured data. Terminal UI operates the selected CLI directly in a PTY. Their internal and CLI values are `visual` and `terminal`.

Claude Code, Codex, Antigravity, Cursor, Qoder, and Grok Build support both modes. Terminal supports terminal mode only, and Cockpit supports native UI only. Changing defaults does not migrate the mode of an existing task.

In Antigravity Native UI, a failed tool item remains marked as failed, but the turn can still complete when the agent recovers and continues its response. When the agent returns an interim answer while waiting for a background command, Cockpit does not close the turn on the CLI success signal alone. It follows the completion notice, later tool calls, and final answer in that same turn. If the agent checks a persistent server and then gives a final answer, the turn can still finish while that server remains running.

## Models and reasoning settings

Models, reasoning levels, service tiers, and system prompts are displayed within the support reported by the capability registry and runtime discovery. Even before a Native UI conversation has any messages, the model selector shows candidates for that task's agent. If runtime candidates arrive later, Cockpit does not revert a valid model selected in the meantime to the default. The CLI and API reject an unverified setting instead of silently substituting another value.

Antigravity Native UI keeps the reasoning level selected for that task after a turn completes and after switching tasks. For example, the next follow-up after a `high` turn remains `high` even when the refreshed candidate list defaults to `low`. Cockpit moves to the current supported default only when the refreshed candidates no longer offer the saved level.

Claude Native UI supplements runtime discovery with built-in candidates that the runtime did not return. The built-in candidates include **Claude Fable 5.1**, with `low`, `medium`, `high`, `xhigh`, and `max` reasoning levels and `high` as its default. Adding a candidate does not change the selected model of an existing task.

Codex model choices, whether built in or discovered at runtime, put newer GPT generations and versions first, then place the standard model before purpose-specific variants of the same version. This makes capability and intended use easier to compare from the top of the list instead of treating model ids alphabetically. Sorting alone never changes the currently selected valid model.

Codex Native UI uses the model catalog discovered from the selected account across the default settings under **Agents**, new tasks, and Autoruns. Changing a pinned account loads that account's catalog again, and a late result from the previous account cannot overwrite it. Model-dependent creation and saving wait while discovery is in progress. If discovery fails, Cockpit shows its built-in candidates and tries discovery again when the surface is reopened or authentication changes. Newly available models can therefore be selected together with the reasoning levels and `fast` support advertised by the discovered catalog.

Codex supports a `standard` or `fast` service tier for applicable models. System prompts are available in native UI for Claude, Codex, Qoder, and Cockpit. `append` preserves Cockpit's standard instructions. `replace` replaces them, leaving Cockpit CLI knowledge available only through an installed skill.

Cockpit Agent model IDs use `openrouter/<id>` for OpenRouter, `opencode-go/<id>` for OpenCode Go, `opencode/<id>` for OpenCode Zen, and `lmstudio/<id>` for LM Studio. OpenCode Go and OpenCode Zen are separate providers, and their **OpenCode Go API Key** and **OpenCode Zen API Key** settings are not interchangeable. Models and tasks for a provider remain unavailable until its key is configured.

Cockpit Agent applies each model's capabilities from the connected provider to new tasks in Desktop and the PWA, the task creation API, and Autoruns. A new task waits for its first model catalog before creation becomes available; if discovery does not settle, known candidates become usable after five seconds. Reasoning uses the model's advertised list first and keeps a saved value while it remains valid. Otherwise Cockpit chooses `medium`, then the first supported value, and leaves reasoning unset for a model that does not support it. The built-in model-id rules are used only when live capability metadata is unavailable.

Register a custom system prompt with `cockpit system-prompt add`; it then appears for new tasks and Autoruns in Desktop and the PWA.

```bash
cockpit system-prompt add reviewer --prompt "Review changes for correctness and clarity."
cockpit system-prompt list
```

Custom prompts are stored as user-owned Markdown in the AGI Tools data area. Their content is sent to the selected agent, so do not include credentials or secrets. Cursor, Grok Build, Antigravity, Terminal, and terminal UI modes do not accept them.

## Accounts and Auto

Claude, Codex, Antigravity, Cursor, Qoder, and Grok Build support a default account and named profiles. Auto is the default for new tasks, Autoruns, and Fleet nodes. It uses shorter usage windows as availability gates, then ranks available accounts from the remaining capacity and reset time of the longest window. It can switch to another available account and resume the saved session after a usage or plan limit.

When you choose Auto or a fixed account at task creation, Cockpit saves that selection and the current runtime account with the task, then uses the same state for its display and next runtime. A fixed account does not switch automatically. Switching an active task stops its current runtime and resumes the saved conversation with the selected profile. Exhausted Claude usage credits and Codex workspace credits are treated as usage limits.

An invalid credential appears as **Session expired** and `authState: expired`; Auto and the Fleet pre-run check exclude it. Signing in again clears the cached verdict and refreshes authentication and usage state.

Antigravity named profiles use browser-based Google OAuth and keep conversations, logs, cache, and usage history under a dedicated home. On macOS, a profile-specific Keychain forms the authentication and quota boundary, and the task does not start if Cockpit cannot verify it first in the search order. The OS keyring is shared on Windows and Linux, so a remaining host login takes precedence over profile token files. Developer shell resources and non-credential settings remain shared with the normal home directory. See [Accounts and Auto](https://agi-labo.com/en/tools/cockpit/docs/accounts) for provenance and safe logout procedures.

## Approval modes

`supervised` asks for approval when a tool action requires it, `accept-edits` permits supported editing operations, and `full-access` permits a broader range of actions. Exact support depends on the agent and UI mode.

Antigravity native UI cannot ask an approval question while it runs, so `supervised` automatically rejects tools that require approval. Choose `accept-edits` or `full-access` for work that needs editing or commands only after checking the objective and risk. An Ask answer does not change the approval mode.

## Resume, usage, and Goals

Supported native UI agents can resume a saved session. A Terminal task cannot restore its former shell process and instead opens a new shell in the same directory. Cursor, Qoder, and Grok Build also restore the connected provider's saved conversation, and an in-progress Grok Build workflow returns as in progress. When an Antigravity Native UI continuation scheduled by a timer or similar condition survives a restart, Cockpit synchronizes it from the saved transcript into its original turn instead of displaying a duplicate new turn.

Provider quota usage and limits appear only when the runtime reports current values. Do not treat an authentication requirement, retrieval failure, or stale update as zero remaining usage.

Context usage follows a separate display contract. If Cursor Native UI does not report token usage, Cockpit estimates the current context from the locally retained conversation and prefixes the value with `~`. The maximum comes from the runtime when available, then from maintained metadata for the selected Cursor model. When neither source has a context length, the maximum is shown as unavailable and no percentage is calculated. If the retained history exceeds the model window, Cockpit caps the displayed active-context estimate at that window and leaves the meter neutral because runtime compaction or history truncation may have reduced the actual active context. These estimates are not provider quota or billing values.

Supported agents use `/goal` to set an objective. Codex can apply a token budget, Qoder can apply a turn limit, and Claude, Codex, Qoder, and Grok Build expose persisted goal state. In Codex Native UI, `/goal` updates the goal state, remains in history as a user message, and starts an actual turn that works toward the objective. Antigravity and Cursor provide a runtime goal-setting operation without a persisted-state display contract.

## Attachments, skills, and external sessions

Every agent has an attachment entry point when created from Desktop, the PWA, or CLI. Whether an image, PDF, Office document, or another file is interpreted natively or passed as a local path depends on the agent, UI mode, and model.

Antigravity Native UI accepts images natively from Desktop and the PWA. When an image is outside the task workspace, Cockpit copies it into a temporary, Git-ignored directory inside that workspace and gives Antigravity the staged path. This makes the image readable in `supervised` mode, but the turn fails with a reason before it starts if the workspace cannot hold the temporary file. Non-image formats such as PDFs and Antigravity Terminal UI are outside this native image-input path.

The Cockpit skill and HTML Mode are installed into supported external agent CLIs. Terminal and Cockpit Agent do not use that skill contract. Claude Code and Codex are the only agents whose external sessions can be imported from Cockpit's history.

## Current capability comparison

The tables below are generated from the same typed capability registry used by task creation and runtime validation. They therefore show current support without maintaining a separate handwritten matrix.

See [Your first task](https://agi-labo.com/en/tools/cockpit/docs/first-task) for task creation, [Reference and support](https://agi-labo.com/en/tools/cockpit/docs/reference-and-support) for configuration and recovery, and [Security and data](https://agi-labo.com/en/tools/cockpit/docs/security-and-data) for data boundaries.
