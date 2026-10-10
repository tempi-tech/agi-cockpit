<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Cockpit CLI

Learn how to connect to AGI Cockpit, inspect JSON results, supervise tasks, request decisions, and operate browser, app, Autorun, Fleet, and Hooks surfaces.

> Verified with AGI Cockpit 4.104.0 on 2026-10-11. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli)

The `cockpit` CLI is the first-party control plane for tasks, surfaces, settings, automation, and local app operations. Commands return JSON so agents and scripts can verify identifiers, state, and errors without parsing screen text.

## Setup and connection

The packaged app installs a common launcher under `~/.agi-tools/bin`. A task started by Cockpit receives connection context automatically, so ordinary commands target the instance that owns the task. Run `cockpit doctor` to inspect the selected instance, runtime files, ports, listeners, and authentication results.

On macOS and Linux, CLI installation and uninstallation change only Cockpit's managed shell block. Other shell settings, existing `PATH` entries, blank lines, and line endings are preserved. If `~/.agi-tools/bin` is already present in a user-managed `PATH` entry, Cockpit does not rewrite the shell file.

The CLI does not silently fall back to another Cockpit instance when the selected one is unavailable. Treat `instance_mismatch` as a target error and inspect the connection instead of resending the command elsewhere.

For local commands, the CLI first authenticates over loopback. If a sandbox blocks loopback or process visibility, it automatically uses file IPC through a directory supplied by that same Cockpit instance. File IPC writes one request ID once and waits for its matching response, so commands such as `task create` and `task send` are not duplicated. No broader sandbox permission is required.

`cockpit doctor` reports `pidVisibility`, `transports.loopback`, `transports.fileIpc`, and `effectiveTransport`. Exit code 7 means neither available transport authenticated. Inspect the per-transport reason and selected instance instead of retrying with elevated permissions.

When the task listener is starting for the first time or recovering, commands such as task, talk, HTML, and side-panel wait for it within a shared 20-second authentication deadline. If the port changes, the CLI follows the new port only after confirming the same PID, token, and IPC directory, and it does not send credentials to the old port. If recovery does not finish within the deadline, the command exits with code 7; inspect `listenerState` and each transport reason in `cockpit doctor`, then rerun the same command.

## Inspect JSON results

Every command returns a JSON object. Check `ok`, identifiers, state fields, and any command-specific result before continuing. A delivered input event is not proof that the target application accepted it; verify the intended postcondition.

For tasks launched by Cockpit, CLI error messages follow the app's display language. After changing the display language, tasks launched afterward use the new language. Automation should branch on a stable `code` when one is available, not on the translated `error` text.

Exit code 7 or `Cannot reach AGI Cockpit` means the app is not reachable through either available local transport, or the installed CLI is too old for the fallback transport. It is not a filesystem-permission error.

Use `--stdin`, `--instruction-file`, or `--text-file` instead of embedding long instructions, Markdown, quotes, backticks, or `$` in a shell argument.

## Run on Windows

The PowerShell `cockpit` launcher decodes responses as UTF-8 and prints exactly one line of JSON for success or failure. An HTTP error preserves the server's JSON body and `code`; a non-JSON HTTP error uses `http_error`, and an unexpected failure uses `unexpected_error`. Scripts can inspect `ok`, `code`, and `error` just as they do on other operating systems instead of parsing a PowerShell error record.

`--stdin` accepts PowerShell pipeline input. PowerShell converts a file to text before the CLI receives it, so specify the encoding with `Get-Content`. Multiple piped strings are joined with line feeds. Use the corresponding `--*-file` option when the exact file text must be preserved. An empty pipeline fails with `stdin_no_input`.

```powershell
Get-Content report.html -Raw -Encoding UTF8 | cockpit html show --stdin
```

The Windows CLI does not require a separate Node.js installation for its internal helpers. The launcher uses the AGI Cockpit executable as its runtime and can find the installed Microsoft Store package even while the app is closed. `cockpit doctor` reports the actual `path` and `source` under `nodeRuntime`. If no runtime is available, commands that need one return `node_runtime_unavailable`; a runtime that returns no JSON produces `node_runtime_failed`.

## Start and supervise tasks

Use `cockpit task create` or `cockpit task run` to start work, `task get` and `task list` to inspect it, `task send` for follow-up instructions, and `task wait --since <seq>` for incremental reports. Supported agents accept `--ui-mode visual|terminal` at creation. Local `create` and `send` accept repeatable `--media` attachments. Use `retry-start` after startup failure, `resume` after a process stops, and `reconnect` for an existing visual session. Only a visual task that currently supports Goals accepts `task goal start`. Use stdin or a file for multiline instructions rather than embedding them in a shell argument. See [Task management (CLI)](https://agi-labo.com/en/tools/cockpit/docs/task-management) for the full parent-child, reporting, follow-up, and completion flow.

Use `cockpit task list --parent <id>` for a lightweight status view of a parent and its children. Add `--recursive` for every descendant and `--all` to include completed tasks. Parent queries and ordinary `--summary` lists omit conversation and model settings and include a response-level `generatedAt` snapshot time.

Inspect pending tool approvals and questions in `task get` under `turnRequests`, then respond with `task approve`, `deny`, or `answer`. `task cancel` stops only the current turn and keeps the task; `compact` compacts its conversation. Because `task clear --confirm` discards the conversation, verify the task and preserve needed history first. `task approval-mode` reads or changes that task's approval mode. See [Task management (CLI)](https://agi-labo.com/en/tools/cockpit/docs/task-management) for the support matrix and procedures.

Completion and deletion are separate. Completing a task can affect its temporary directory or Worktree, while deleting removes task history and associated local state. Confirm the exact ID and required artifacts before destructive or bulk actions.

## Distinguish Ask, display, and HTML

`cockpit ask` waits for a human answer and resumes the same task after that answer. `cockpit display` presents information without waiting. `cockpit html` stores a generated HTML Surface for the task.

After creating an Ask, end the current turn and wait. Do not poll for the answer or continue work that depends on it. An Ask answer is not itself permission from the operating system or an external service.

## Operate browser and App Surfaces

Use `cockpit browser` for web pages and `cockpit app` for an already-running Android target or iOS Simulator. Confirm the Browser Identity, and verify browser clicks and submissions with postconditions such as URL, text, element state, or network activity. In App Surface, prefer accessibility labels and verify a fresh snapshot or explicit expectation after input.

External links, uploads, physical devices, and secret input have additional safety boundaries. Do not resend an already delivered action without checking state because it can create a duplicate operation.

`cockpit secret request` asks Desktop or an HTTPS-connected PWA to fill one specified browser password field once. The CLI identifies the purpose and destination and can inspect or cancel the request with `status` and `cancel`, but it has no value argument, value stdin, or retrieval command. The person enters the value in a Cockpit surface. Success and failure responses include the selected `instance`; a failure also returns a stable `code` and descriptive `error`. Automation should branch on `code`, not translated prose, and check both the instance and reason before retrying. See [Security and data](https://agi-labo.com/en/tools/cockpit/docs/security-and-data#enter-a-password-once) for the conditions and boundaries and the [`cockpit secret` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/secret) for exact codes and recovery.

See [cockpit browser](https://agi-labo.com/en/tools/cockpit/docs/browser), [Browser Identity](https://agi-labo.com/en/tools/cockpit/docs/browser-identities), and [App Surface](https://agi-labo.com/en/tools/cockpit/docs/app-surface) for practical workflows.

## Use Autorun and Fleet

`cockpit autorun` starts a new task or sends instructions to an existing task once, on an interval, or from cron. Membership is checked both when the Autorun is created and when it runs. If a saved runtime setting becomes unavailable, Cockpit disables the Autorun instead of silently choosing another runtime.

`cockpit fleet` executes dependency-aware tasks as a Run. It covers YAML validation, gates, retries, resume, Run titles, and node progress. For a completed, failed, stopped, or paused Run, use `complete-tasks --dry-run` to inspect targets before completing its remaining tasks in bulk. Use Autorun for simple scheduled starts and Fleet for dependent multi-step work. See [Fleet](https://agi-labo.com/en/tools/cockpit/docs/fleet) for the practical workflow and the [`cockpit fleet` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/fleet) for exact syntax.

## Configure credentials and the Cockpit provider

Rename a named agent account profile by its current name or profile ID:

```bash
cockpit accounts rename work "Work (EU)" --agent-type codex
```

Only the display name changes; the ID, credentials, and task and Autorun assignments remain intact. The default account cannot be renamed. Fleet YAML resolves `account: <name>` by name when a run starts, so update any Fleet that still refers to the old name. See [Accounts and Auto](https://agi-labo.com/en/tools/cockpit/docs/accounts#rename-a-profile) for validation rules and the Settings workflow.

`cockpit settings` can save or remove OpenRouter, OpenCode Go, OpenCode Zen, and Anthropic API keys in encrypted storage. Never put a key in a command argument; use only `--stdin` or `--key-file`. Reads report presence without returning the value.

```bash
cockpit settings set agents.credential.openrouter --stdin
cockpit settings reset agents.credential.openrouter
cockpit settings set agents.provider.cockpit openrouter
```

Choose the Cockpit Agent provider from `openrouter`, `opencode-go`, `opencode`, or `lmstudio`. A provider that requires a key can be selected or restored as the default only after its corresponding credential is stored. These settings operate only on the local Cockpit.

`cockpit settings refresh-models lmstudio` bypasses the cache and reads a fresh model list from the saved LM Studio URL. It does not start, stop, or reload a model and does not change settings. An unavailable endpoint returns `validation_unavailable`.

## Automate reactions with Hooks

`cockpit hooks` runs local actions in response to task completion, Asks, Autoruns, Fleet Runs, app startup and shutdown, or hotkeys. See [Hooks](https://agi-labo.com/en/tools/cockpit/docs/hooks) for registration, testing, filters, and management in Settings. The [`cockpit hooks` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/hooks) covers all events, options, and command results.

## Read usage and reset Codex capacity

`cockpit usage` reads usage for supported providers and accounts. It also reports available Codex reset credits and their expiration dates without spending them. `cockpit usage reset --agent-type codex [--account <name>] --confirm` consumes one finite credit to clear that account's current rate-limit window. This cannot be undone or refunded; inspect the account and available credits before using `--confirm`. See the [`cockpit usage` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/usage) for outcomes and exit codes.

## Distinguish local and remote targets

`cockpit devices` lists this computer and devices on the same tailnet that answer Cockpit's health check, including their operating systems. A device that is online in Tailscale but does not answer Cockpit is omitted from the list and reported with a reason under `diagnostics.unreachable`. Devices that Tailscale reports as offline or without an address are counted under `diagnostics.notProbed`, so do not treat an empty `devices` array as conclusive without checking diagnostics. `cockpit devices self` skips discovery and returns only this computer and aliases.

Supported `task`, `autorun`, `accounts`, `fleet`, and `hooks` commands can target a device host or saved alias through `--host`. `ask list`, `ask answer`, and `ask close` also support remote targets, while Ask creation and relay configuration remain local. Commands without host support, including browser, App Surface, display, settings, usage, and update operations, affect only the local Cockpit instance.

Remote control needs no token from a computer on the same tailnet with the same Tailscale owner; other connections use the paired bearer token. When the target is Tailscale-only, connections that are neither a Tailscale peer nor loopback are refused. File paths and directories are resolved on the target computer. Remote responses include the device identity and do not fall back to local file IPC.

Stopping Remote Access, clearing Identity data, deleting an account, and uninstalling the CLI require explicit confirmation flags. A confirmation flag is not a substitute for user authorization; verify the requested action and exact target first.

## Command reference

The references below are generated at build time from `electron/cockpit-docs/help`, the same canonical source used by in-app `cockpit help [topic]`. Creative workflows are outside the scope of the official guides, but existing related commands remain in the generated reference to preserve CLI completeness.
