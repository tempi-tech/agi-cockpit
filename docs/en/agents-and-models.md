<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Agents and models

Compare eight agents, native and terminal UI, models, reasoning levels, accounts, approvals, resume behavior, and usage reporting.

> Verified with AGI Cockpit 4.102.0 on 2026-10-09. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/agents-and-models)

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

## Choose the default agent

Open **Settings → Agents → Common** and choose **Default agent** from the dropdown. This is the agent initially selected when creating a new task; it does not change an existing task's agent. Shared agent settings are grouped under Common, while provider-specific model and account settings remain under each agent.

## Choose with Smart routing

Smart routing is for AGI Labo members. If you select it from Desktop **New task** or Quick Task while signed out, Cockpit explains the feature and offers **View membership plans** and **Sign in with a member account**. After signing in as a member and enabling it, Cockpit chooses a valid combination from the instruction, configured agents, discovered models and reasoning levels, recent and active project workspaces, the Master workspace, and temporary or persistent workspaces. External agents are created in Native UI with an Auto account. Terminal and Creative Studio are not routed automatically.

Use **Routing policy** for free-form preferences, such as prioritizing Codex for implementation and Claude for research or writing. The policy and on/off state are stored on Desktop for each signed-in user, save automatically, and apply to later tasks. Manually selecting a workspace fixes only the workspace. Turn off Smart routing when you want to choose the agent or model directly.

Candidates are limited to current CLI detection, model discovery, and configured Cockpit Agent providers. If a requested candidate is unavailable, check the provider and API key or revise the policy. Smart routing does not silently substitute an unconfigured agent or model.

## Native UI and terminal UI

Native UI lets Cockpit display conversation, tool execution, usage, model, and approval state as structured data. Terminal UI operates the selected CLI directly in a PTY. Their internal and CLI values are `visual` and `terminal`.

Claude Code, Codex, Antigravity, Cursor, Qoder, and Grok Build support both modes. Terminal supports terminal mode only, and Cockpit supports native UI only. Changing defaults does not migrate the mode of an existing task.

On Windows, the Antigravity and Grok Build launch-command fields under **Agents** accept a full executable path that contains spaces. Cockpit accepts the path quoted, prefixed with PowerShell's `&`, or unquoted when that executable exists. Working and additional project folders passed to Antigravity also remain one argument each when their paths contain spaces. Launch behavior on macOS and Linux is unchanged.

In Antigravity Native UI, a failed tool item remains marked as failed, but the turn can still complete when the agent recovers and continues its response. When the agent returns an interim answer while waiting for a background command, Cockpit does not close the turn on the CLI success signal alone. It follows the completion notice, later tool calls, and final answer in that same turn. If the agent checks a persistent server and then gives a final answer, the turn can still finish while that server remains running.

## Use folders across a project

Every task starts in one working folder. When the task belongs to a project with more folders, Cockpit passes the current folder list to the agent each time its session starts or resumes. Direct multi-folder access depends on the agent and UI mode: supported Native UI and terminal UI combinations receive access arguments or runtime permissions, while some combinations receive only a note containing the paths. Terminal tasks do not receive project-folder access. Codex terminal UI in full-access mode is not given the other paths.

Adding or removing a project folder does not silently change a session that is already running. Check the task header for the folder set that session received, then reconnect or resume when the new set must apply. See the [project CLI reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/project#folders-a-task-can-use) for the current agent-by-agent behavior.

## Models and reasoning settings

Models, reasoning levels, service tiers, and system prompts are displayed within the support reported by the capability registry and runtime discovery. The model picker groups candidates under agent tabs and opens on the agent currently used by the task. In task details, choosing a model under another supported agent schedules an agent switch for the next message. Even before a Native UI conversation has any messages, the picker shows candidates for that task's agent. If runtime candidates arrive later, Cockpit does not revert a valid model selected in the meantime to the default. The CLI and API reject an unverified setting instead of silently substituting another value.

Antigravity Native UI keeps the reasoning level selected for that task after a turn completes and after switching tasks. For example, the next follow-up after a `high` turn remains `high` even when the refreshed candidate list defaults to `low`. Cockpit moves to the current supported default only when the refreshed candidates no longer offer the saved level.

Claude Native UI supplements runtime discovery with built-in candidates that the runtime did not return. The built-in candidates include **Claude Fable 5.1**, with `low`, `medium`, `high`, `xhigh`, `max`, and `ultracode` reasoning levels and `high` as its default. The picker keeps **Default** with its resolved model label and a separate row for selecting that resolved model directly. Default follows later changes to Claude's default, while the direct row fixes the choice to that model. Adding a candidate does not change the selected model of an existing task.

Claude's **Ultracode** reasoning level runs Claude Code's ultracode mode: `xhigh` reasoning plus multi-agent workflows on every substantial task, which uses much more of your allowance. The picker offers it on models that support `xhigh` when the installed Claude Code reports ultracode as available, and hides it when dynamic workflows are turned off in Claude Code. Like other Claude reasoning changes, switching an existing task to Ultracode takes effect when its session next starts. To use multi-agent workflows for a single turn instead, include the word `ultracode` in a message you type in Cockpit, as in Claude Code.

Codex model choices, whether built in or discovered at runtime, put newer GPT generations and versions first, then place the standard model before purpose-specific variants of the same version. This makes capability and intended use easier to compare from the top of the list instead of treating model ids alphabetically. Sorting alone never changes the currently selected valid model.

Codex Native UI uses the model catalog discovered from the selected account across the default settings under **Agents**, new tasks, Autoruns, and running tasks. A new task can start with its current valid selection without waiting for discovery to finish. Cockpit shows the last successful list for that account immediately across app restarts and refreshes it in the background. A failed or empty refresh keeps the previous list. Changing a pinned account discards only that profile's previous list before loading the new account, and a late result from the previous account cannot overwrite it. Token expiration or a cancelled sign-in does not by itself discard the list for the same profile. Before that account has any authoritative list, a screen may show built-in candidates or a fetched default-account list as an unconfirmed fallback. It does not treat that fallback as the selected account's result or promise that the account can run it. Newly available models become selectable after the background update, with the reasoning levels and `fast` support advertised by discovery.

For an existing Codex task, if discovery fails or reaches its 20-second deadline, Cockpit also adds the model saved with that task to the fallback candidates and preserves the saved reasoning level and service tier. The picker marks availability as unconfirmed, so these candidates are not a promise that the account can run them. **Reload models** retries with that task's account. A result that arrives after the deadline still replaces the unconfirmed candidates with the discovered catalog and capabilities.

Codex supports `standard`, `fast`, and `ultrafast` service tiers. The picker shows `fast` or **Ultrafast** only when the selected model's discovered capabilities advertise that tier; unsupported explicit values are rejected. System prompts are available in native UI for Claude, Codex, Qoder, and Cockpit. `append` preserves Cockpit's standard instructions. `replace` replaces them, leaving Cockpit CLI knowledge available only through an installed skill.

Cockpit Agent model IDs use `openrouter/<id>` for OpenRouter, `opencode-go/<id>` for OpenCode Go, `opencode/<id>` for OpenCode Zen, and `lmstudio/<id>` for LM Studio. OpenCode Go and OpenCode Zen are separate providers, and their **OpenCode Go API Key** and **OpenCode Zen API Key** settings are not interchangeable. Models and tasks for a provider remain unavailable until its key is configured.

The bundled OpenCode runtime supports free models included in OpenCode Zen's live model catalog. Free models still require an OpenCode Zen API key, and the models, terms, and allowances shown depend on Zen's current offering.

In Cockpit Agent settings, **Reload models** appears beside the default model when an LM Studio model is selected and beside the LM Studio URL even when another provider is selected. Settings uses the currently edited URL, including an unsaved value. The CLI command `cockpit settings refresh-models lmstudio` retrieves a fresh list from the saved URL. This operation only lists models: it does not start, stop, or reload a model, and it does not interrupt a running task. Cockpit keeps the current selection instead of silently replacing it. If a successful response no longer includes that model, Settings warns and blocks Save; a connection failure keeps the selection and shows retry guidance.

Cockpit owns the local OpenCode servers that it starts for OpenCode Go or OpenCode Zen. Normal task cleanup stops task-scoped resources, and quitting Cockpit also stops any owned servers that remain. An OpenCode server started independently outside Cockpit is not part of this shutdown.

From the CLI, choose the Cockpit Agent provider with `cockpit settings set agents.provider.cockpit <provider>`, using `openrouter`, `opencode-go`, `opencode`, or `lmstudio`. Save an API key to encrypted storage with `cockpit settings set agents.credential.<name> --stdin` or `--key-file`, and remove it with `settings reset agents.credential.<name>`. A provider that requires a key cannot be selected until its corresponding credential is present.

Cockpit Agent applies each model's capabilities from the connected provider to new tasks in Desktop and the PWA, the task creation API, and Autoruns. When a valid model is already resolved, a new task can start while its catalog is still loading. Only a first use with no resolved selection waits for usable candidates; if discovery does not settle, known candidates become usable after five seconds. Reasoning uses the model's advertised list first and keeps a saved value while it remains valid. Otherwise Cockpit chooses `medium`, then the first supported value, and leaves reasoning unset for a model that does not support it. The built-in model-id rules are used only when live capability metadata is unavailable.

Register a custom system prompt with `cockpit system-prompt add`; it then appears for new tasks and Autoruns in Desktop and the PWA.

```bash
cockpit system-prompt add reviewer --prompt "Review changes for correctness and clarity."
cockpit system-prompt list
```

Custom prompts are stored as user-owned Markdown in the AGI Tools data area. Their content is sent to the selected agent, so do not include credentials or secrets. Cursor, Grok Build, Antigravity, Terminal, and terminal UI modes do not accept them.

### Check agent CLI versions and update status

Under **Settings → Updates**, **Agent CLIs** shows the installed version, latest version Cockpit could check, and update status for Claude, Codex, Antigravity, Cursor, Qoder, and Grok Build. **Check** fetches the current status instead of reusing the saved result. When an update can be run, **Update CLI** opens a terminal that runs that CLI's update command on this device. Cockpit omits the update action when the installed CLI is already current and reports the state as unknown when it cannot obtain or compare the versions.

From the CLI, `cockpit setup agent status <agent>` reads the command, installed version, available latest version, `updateAvailable`, and changelog URL. `updateAvailable` is `true` when an update exists, `false` when the installed version is current or newer, and `null` when the versions cannot be compared. This command is read-only and never updates the agent CLI.

### Check and reload the model list

Model controls in agent settings, new tasks, and Autoruns show where the list came from, retrieval status, and when it was fetched. After a failed or timed-out CLI lookup, use **Show reason** when available, then **Reload models** to try again. Built-in candidates or another fetched list may appear as an unconfirmed fallback, but that does not guarantee that your installed CLI or selected account can use them. Codex keeps authoritative catalogs separated by account and never treats fallback candidates as fetched for the selected account. An existing task also adds its saved selection to the fallback.

When discovering Grok Build models, Cockpit does not start the Grok CLI or open a sign-in page if discovery would require interactive authentication. While signed out, it shows built-in candidates and guidance to sign in from Settings for the current catalog. If an expired access token has a usable refresh token, model discovery also uses known or built-in candidates instead of starting a refresh. The Grok CLI refreshes the token when an actual task starts.

If a model is missing or the CLI is outdated, review the installed and available CLI versions in the notice. Desktop offers **Update CLI**, which opens a terminal for that agent’s update command. After a successful update, reload the models. In the PWA, copy the displayed command and run it on the computer hosting Cockpit; the PWA does not run the CLI update. Except for unlisted Codex models, if the CLI is already current, choose another available model. If its latest version is unknown, check whether a newer CLI release adds the model.

When switching agents, the model-list status uses previously retrieved information for the selected agent and account when available. In the PWA task creation dialog, look below the agent and model controls for the model-list source, retrieval status, and related notices.

For CLI agents, Cockpit saves the last successfully retrieved model list per account, shows it immediately when a screen opens or the app restarts, and refreshes it in the background. A failed or empty refresh keeps the previous list, and repeated failures back off before another automatic attempt. Cockpit does not launch an agent process merely to discover models when that CLI is not installed. It discards only the profile whose account actually changed. Select **Reload models** when the list is outdated. If a custom catalog is active, check the catalog configuration below as well as the CLI version.

### Custom Codex catalogs and unlisted models

When a Codex account uses `model_catalog_json` in `config.toml`, the model list shows that it uses a custom catalog and displays its path. **Copy catalog path** identifies the file. To add missing models, edit that catalog or remove `model_catalog_json` from the configuration, then select **Reload models**. Cockpit does not modify the catalog file itself.

A named Codex account keeps an isolated configuration and a newly created account does not inherit `model_catalog_json` from the default account. When Cockpit provisions an existing named account, it removes an absolute catalog setting that points inside the default Codex home from that account's `config.toml` and saves the original once as `config.toml.before-model-catalog-migration`. Relative paths, catalogs outside the default home, and the default account's configuration remain unchanged. Reconnect an already running session to apply the change; a new session uses the isolated configuration when it starts.

For a Codex model missing from the retrieved list whose capabilities are unknown, the reasoning picker offers `low`, `medium`, `high`, `xhigh`, `max`, and `ultra`. This does not guarantee that the model supports every level; Codex determines availability at runtime. Models with known capabilities use their advertised choices.

## Accounts and Auto

Claude, Codex, Antigravity, Cursor, Qoder, and Grok Build support a default account and named profiles. Auto is the default for new tasks, Autoruns, and Fleet nodes. It uses shorter usage windows as availability gates, then ranks available accounts from the remaining capacity and reset time of the longest window. It can switch to another available account and resume the saved session after a usage or plan limit.

When you choose Auto or a fixed account at task creation, Cockpit saves that selection and the current runtime account with the task, then uses the same state for its display and next runtime. A fixed account does not switch automatically. Switching an active Claude, Codex, Antigravity, Cursor, Qoder, or Grok Build task stops its current runtime and carries the saved conversation into the selected profile before resuming. A missing, busy, unreadable, or ambiguous source fails safely; different target history is archived before replacement. Exhausted Claude usage credits and Codex workspace credits are treated as usage limits.

An invalid credential appears as **Session expired** and `authState: expired`; Auto and the Fleet pre-run check exclude it. An expired Grok Build access token with a refresh token remains selectable with `authState: ok` and is renewed when the next task starts. Signing in again clears the cached verdict and refreshes authentication and usage state.

Antigravity named profiles use browser-based Google OAuth and keep conversations, logs, cache, and usage history under a dedicated home. On macOS, a profile-specific Keychain forms the authentication and quota boundary, and the task does not start if Cockpit cannot verify it first in the search order. The OS keyring is shared on Windows and Linux, so a remaining host login takes precedence over profile token files. Developer shell resources and non-credential settings remain shared with the normal home directory. See [Accounts and Auto](https://agi-labo.com/en/tools/cockpit/docs/accounts) for provenance and safe logout procedures.

## Approval modes

`supervised` asks for approval when a tool action requires it, `accept-edits` permits supported editing operations, and `full-access` permits a broader range of actions. Exact support depends on the agent and UI mode.

Antigravity native UI cannot ask an approval question while it runs, so `supervised` automatically rejects tools that require approval. Choose `accept-edits` or `full-access` for work that needs editing or commands only after checking the objective and risk. An Ask answer does not change the approval mode.

## Resume, usage, and Goals

Supported native UI agents can resume a saved session. A Terminal task cannot restore its former shell process and instead opens a new shell in the same directory. Cursor, Qoder, and Grok Build also restore the connected provider's saved conversation, and an in-progress Grok Build workflow returns as in progress. When an Antigravity Native UI continuation scheduled by a timer or similar condition survives a restart, Cockpit synchronizes it from the saved transcript into its original turn instead of displaying a duplicate new turn.

Provider quota usage and limits appear only when the runtime reports current values. Do not treat an authentication requirement, retrieval failure, or stale update as zero remaining usage.

For Claude, Desktop and the CLI can show the unused limit-reset count and expiration dates. Desktop, the PWA, and the CLI can show this month's extra-usage spend and monthly limit. Both appear only when Claude reports them and are separate from regular quota windows. See [Accounts and Auto](https://agi-labo.com/en/tools/cockpit/docs/accounts#check-remaining-quotas) for omission rules and CLI fields.

The Desktop and PWA new-task screens also follow the selected Claude Code, Codex, Antigravity, Cursor, Qoder, Grok Build, or Cockpit agent when showing supported allowances. A fixed account shows that profile; Auto shows the account selected by the same logic used for task creation. Changing the agent or account changes the preview. Terminal has no provider-usage display.

Context usage follows a separate display contract. If Cursor Native UI does not report token usage, Cockpit estimates the current context from the locally retained conversation and prefixes the value with `~`. The maximum comes from the runtime when available, then from maintained metadata for the selected Cursor model. When neither source has a context length, the maximum is shown as unavailable and no percentage is calculated. If the retained history exceeds the model window, Cockpit caps the displayed active-context estimate at that window and leaves the meter neutral because runtime compaction or history truncation may have reduced the actual active context. These estimates are not provider quota or billing values.

Supported agents use `/goal` to set an objective. Codex can apply a token budget, Qoder can apply a turn limit, and Claude, Codex, Qoder, and Grok Build expose persisted goal state. When a visual task reports `goalCommandAvailable: true`, `cockpit task goal start <id> --objective "..."` or `--objective-file` starts the same Goal. In Codex Native UI, starting a Goal updates the goal state, remains in history as a user message, and starts an actual turn that works toward the objective. Antigravity and Cursor provide a runtime goal-setting operation without a persisted-state display contract. The CLI cannot stop or clear a Goal.

## Attachments, skills, and external sessions

Every agent has an attachment entry point when created from Desktop, the PWA, or CLI. Whether an image, PDF, Office document, or another file is interpreted natively or passed as a local path depends on the agent, UI mode, and model.

Antigravity Native UI accepts images natively from Desktop and the PWA. When an image is outside the task workspace, Cockpit copies it into a temporary, Git-ignored directory inside that workspace and gives Antigravity the staged path. This makes the image readable in `supervised` mode, but the turn fails with a reason before it starts if the workspace cannot hold the temporary file. Non-image formats such as PDFs and Antigravity Terminal UI are outside this native image-input path.

The Cockpit skill and HTML Mode are installed into supported external agent CLIs. Terminal and Cockpit Agent do not use that skill contract. Claude Code and Codex are the only agents whose external sessions can be imported. The import appears at the end of onboarding and as **Continue where you left off** on an empty task board; imported tasks resume in Terminal UI.

## Current capability comparison

The tables below are generated from the same typed capability registry used by task creation and runtime validation. They therefore show current support without maintaining a separate handwritten matrix.

See [Your first task](https://agi-labo.com/en/tools/cockpit/docs/first-task) for task creation, [Reference and support](https://agi-labo.com/en/tools/cockpit/docs/reference-and-support) for configuration and recovery, and [Security and data](https://agi-labo.com/en/tools/cockpit/docs/security-and-data) for data boundaries.
