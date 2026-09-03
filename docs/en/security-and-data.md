<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Security and data

Understand local execution, external and Ask-relay transmission, approvals, Cockpit Hooks, credentials, attachments, Browser Identities, and Remote Access storage boundaries.

> Verified with AGI Cockpit 4.69.0 on 2026-09-04. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/security-and-data)

AGI Cockpit runs tasks and agent processes on your computer. Features still communicate with external services when required, including the selected AI provider, websites opened in the browser, AGI Labo authentication and membership checks, and anonymous usage events.

## What stays local

Core Cockpit data, including task state, conversation history, Autoruns, Fleets, Cockpit Hook definitions and run history, templates, CLI runtime information, and logs, is stored under `~/.agi-tools/data/cockpit`. Some data, such as attachments and Electron browser profiles, is stored in the operating system's application-data area. Working files live in the selected project, temporary directory, or Git Worktree. An image from outside the workspace that is sent to Antigravity Native UI also has a temporary readable copy inside that workspace.

The agent process reads and writes its workspace. Depending on the approval mode and agent permissions, it may access files not currently displayed in Cockpit. Select only the directories needed for the task.

## What is sent externally

The selected agent, UI mode, model, and tools determine which instructions, conversations, attachments, file content, and tool results are sent to an AI provider. Cockpit Agent uses the configured OpenRouter, OpenCode Go, OpenCode Zen, or LM Studio endpoint. OpenCode Go and OpenCode Zen use separate API keys. Whether LM Studio is local or remote depends on its configured URL.

Sites opened in the in-app browser receive normal browser traffic such as input, uploads, cookies, and WebAuthn. Remote Access transfers task, Ask, and Autorun information between the connected device and Cockpit.

Enabling a Discord or Slack relay under **Settings → Ask notifications** sends the Ask summary, questions, choices, task name, a link back to Cockpit, and optional Ask images or videos to the selected channel. Members who can read that channel can see the post, but Cockpit accepts an answer only from the single configured `allowedUserId`. Relays are off by default and make outbound connections from Cockpit to the Discord Gateway or Slack Socket Mode.

## Anonymous data in guest use

Packaged guest use sends a random installation ID, app version, a daily session check, and onboarding events for steps reached, abandonment, guest selection, and sign-in to AGI Backend. Task instructions, conversations, file content, and project names are not included in these anonymous events.

An onboarding abandonment event that could not be sent is kept temporarily in the OS application-data area for retry. Anonymous events are sent only for guest use of a packaged app. Signed-in use sends the app version during authentication session checks.

## Distinguish approvals from Ask

`supervised`, `accept-edits`, and `full-access` define the boundary of agent tool operations. When one-time approval, always allow, or deny is offered, inspect the operation, path, command, and external destination. “Always allow” affects later operations of the same kind, so prefer one-time approval when the scope is unclear.

Ask returns a human policy decision; it is not a tool approval. Choosing “publish” in an Ask does not automatically grant permissions required by the operating system, an external service, or another tool.

## Run Cockpit Hooks safely

Cockpit Hooks automatically run a registered shell action with the user's local permissions. They are separate from an agent's approval mode and do not sandbox the hook action. Register only scripts that you understand and control, then test them explicitly with `cockpit hooks test` before enabling them. To prevent arbitrary code from being registered remotely, `cockpit hooks` rejects `--host`.

The complete event JSON is delivered on standard input. Some values, including task names, working directories, Ask summaries, and selected text, are also available through environment variables. A hook that invokes an external command or network service can transmit those values. Select only the events and filters you need, and do not write tokens, personal data, or local paths to standard output or standard error. Their final 4 KB is stored in run history and can be inspected from Settings or the CLI.

Settings can disable or remove a hook, but removing it leaves existing run history in place. History rotates at 2,000 lines or 2 MB and keeps the latest 1,000 lines. Capturing selected text from a global hotkey on macOS requires Accessibility permission. Without it, the hook still runs with an empty selection value. Concurrency limits, timeouts, depth limits, and the circuit breaker reduce runaway execution; they do not make a registered command safe.

## Store credentials

AGI Cockpit tokens and API keys are stored in encrypted storage such as the OS Keychain or keyring. The Discord bot token and Slack bot and app-level tokens use this same boundary and are not returned as ordinary setting values or in status output. If secure storage is unavailable, Cockpit refuses to save rather than falling back to plaintext. Credentials that fail to delete are disabled and scheduled for deletion again on the next launch.

Named agent profiles isolate authentication. Antigravity keeps conversations, logs, cache, and usage history under a profile-specific home. On macOS, each profile stores its OAuth token in a dedicated Keychain, and Cockpit verifies that Keychain is first in the search order before starting a task. The host login Keychain remains later in the list for tools such as GitHub CLI, but Antigravity resolves the profile Keychain placeholder or token first. On Windows and Linux, the OS keyring itself is shared, so its host login is used by every profile. To rely on separate profile token files there, first log the default account out of the shared login.

Antigravity `accounts logout` previews every target and shared impact without `--confirm`. Running it for a named profile never removes the host login; when that profile uses shared authentication, the result points to the default-account logout instead. Logging out the default can also affect other profiles, ordinary terminal-launched Agy processes, and Gemini CLI authentication under the same home. Cockpit never logs an account out automatically. Browser Identities remain separate from agent account profiles.

## Isolate Browser Identities

Each Browser Identity persists its own cookies, cache, localStorage, permissions, proxy authentication, and browser sessions. A task and Autorun can each be assigned one Identity; the Default Identity is used when none is selected.

On macOS, `import-session` imports cookies belonging to the visible site's registrable domain and localStorage for the exact origin from Chrome into the selected Identity. It does not import sessionStorage, IndexedDB, extension state, device-bound authentication, or passkeys themselves.

Clearing an Identity closes its sessions. Removing it also deletes persistent data. An Identity referenced by a running task or Autorun cannot be removed without a replacement, and the Default Identity cannot be removed.

See [Browser Identity](https://agi-labo.com/en/tools/cockpit/docs/browser-identities) for assignment and removal procedures.

## Handle attachments and Ask media

Attachments use randomized stored names in a Cockpit-managed area and are validated by extension, MIME type, actual size, and content. One message accepts up to eight files, 512 MB each and 1 GB total; JSON is limited to 25 MB. Archives and executable formats are rejected.

Antigravity Native UI handles image attachments from Desktop and the PWA directly. When an image is outside the workspace, Cockpit copies it under `.agi-cockpit-attachments` in the canonical workspace, using a random per-session directory whose contents are excluded by `.gitignore`. An image already inside the workspace is not copied. Cockpit refuses to follow a symlink or another non-directory staging root, removes that session's copies when the session, CLI, or app stops, and removes unowned directories older than 24 hours when another session starts in the same workspace.

This staging does not expand Antigravity's `supervised` boundary to read arbitrary files outside the workspace. It explicitly prepares an image inside the workspace before Antigravity reads it. A sensitive image therefore exists temporarily inside the working project, and the turn fails if Cockpit cannot write the staging copy there.

When the relay option to attach files posted in Discord or Slack is enabled, Cockpit downloads files posted by the allowed user in the configured channel into managed storage and attaches them to the next response for the matched Ask. A reply targets that Ask; otherwise the file is assigned to the newest unanswered Ask in the same channel. Disable this option when it is unnecessary, and do not post sensitive files to a shared channel.

File names and content are not trusted instructions. Chat opens only safe formats within the managed area and does not directly launch executables, unmanaged paths, remote `file` URLs, or data URLs that could contain executable content. Remove personal information, local paths, tokens, and session data before external sharing.

Fleet command gates save complete stdout and stderr per attempt under the Run's `fleet-runs/<runId>/gates/` directory, retaining only the head and tail when a log exceeds 20 MB. Output may contain tokens, local paths, test fixtures, or personal data. Inspect it before sharing from the Fleet panel or `cockpit fleet output`. Before removing an unneeded terminal Run, preserve only the diagnostic evidence that is still required in a safe location.

## Protect Remote Access

The recommended configuration is Tailscale-only access over HTTPS. Cockpit authenticates devices with Tailscale device information and a six-digit pairing code, with expiration and failure limits. It does not silently downgrade to HTTP when an HTTPS certificate cannot be obtained.

Local Wi-Fi mode is unencrypted and activates only after explicit confirmation. Do not use it on public or untrusted networks. Stopping Remote Access ends connected sessions and saves standalone state.

See [Remote Access](https://agi-labo.com/en/tools/cockpit/docs/remote-access) for setup details.

## Protect App Surface

App Surface attaches one target exclusively to one task. The first attachment to an Android physical device requires Ask approval, and `fill` refuses secure fields. The last frame may remain after disconnection, but the stale Surface disables interaction.

Cockpit does not start, stop, or install the target app. Completing or deleting a task detaches the target without closing its app.

See [App Surface](https://agi-labo.com/en/tools/cockpit/docs/app-surface) for attachment and operation procedures.

## Confirm completion and deletion

Completing a temporary-directory task deletes its workspace. Completing a Git Worktree task in Desktop preserves its Worktree, while CLI `task complete` removes it by default. Deleting tasks, bulk-deleting a Fleet Run and its tasks, or removing an Identity can destroy history and local data.

Before deletion, confirm the target ID, path, Git state, required artifacts, and recovery method. Obtain user approval immediately before publication, external transmission, purchase, or permission changes.
