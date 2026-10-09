<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Security and data

Understand local execution, external and Ask-relay transmission, approvals, Cockpit Hooks, credentials, attachments, Browser Identities, and Remote Access storage boundaries.

> Verified with AGI Cockpit 4.103.0 on 2026-10-10. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/security-and-data)

AGI Cockpit runs tasks and agent processes on your computer. Features still communicate with external services when required, including the selected AI provider, websites opened in the browser, AGI Labo authentication and membership checks, and anonymous usage events.

## What stays local

Core Cockpit data, including task state, conversation history, Autoruns, Fleets, Cockpit Hook definitions and run history, templates, CLI runtime information, and logs, is stored under `~/.agi-tools/data/cockpit`. Some data, such as attachments and Electron browser profiles, is stored in the operating system's application-data area. Working files live in the selected project, temporary directory, or Git Worktree. An image from outside the workspace that is sent to Antigravity Native UI also has a temporary readable copy inside that workspace.

The agent process reads and writes its workspace. Depending on the approval mode and agent permissions, it may access files not currently displayed in Cockpit. Select only the directories needed for the task.

Cockpit owns the local OpenCode servers that it starts for Cockpit Agent connections to OpenCode Go or OpenCode Zen and stops them when Cockpit quits. It does not stop a server that the user started independently outside Cockpit.

From v4.90.0, Cockpit Agent stores its conversation database at `opencode/v1/opencode.db` inside Cockpit's data directory. On first startup, it copies an existing legacy OpenCode database to preserve conversations. That source can include conversations from OpenCode used outside Cockpit. The original database is left unchanged, and subsequent conversations do not sync between the two databases. If compatibility cannot be verified, Cockpit shows an error and stops Cockpit Agent startup.

PWA task search queries and recent searches are stored in the current browser’s localStorage separately for each connected host. They do not sync to other devices. **Clear search** and **Clear history** are separate actions; use both to remove both the current query and history.

Smart routing's on/off state and **Routing policy** are stored in Desktop localStorage separately for each signed-in user ID. They do not sync to another device.

## Project folders and agent access

A project can contain multiple folders. When an agent session starts or resumes, Cockpit passes the project’s other folders to the agent according to that agent’s supported access options. Adding a folder can therefore expand the files the agent can read or write; review the folder list before reconnecting a task. Approval mode and agent-specific restrictions still apply. A Git Worktree replaces only its source folder; other project folders are used in place. See the [project CLI reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/project) for agent-specific behavior.

Deleting a project removes its grouping only: tasks and folders remain. A task folder registered in a project is preserved when its task is completed or deleted.

## Importing existing sessions

**Continue where you left off**, at the end of onboarding and on an empty task board, lists up to 200 Claude Code and Codex sessions from the last 30 days, grouped by workspace. Select sessions individually or by workspace, then import them together, or skip. Workspace suggestions can also be registered using the existing project creation dialog. Imported tasks resume in Terminal UI.

Scanning and importing happen locally. Neither conversation content nor workspace paths are sent externally by the import. Original session files are only read: importing never changes or deletes them, and does not start an agent. Anonymous onboarding events contain only the step, import count, or skip action.

## What is sent externally

The selected agent, UI mode, model, and tools determine which instructions, conversations, attachments, file content, and tool results are sent to an AI provider. Cockpit Agent uses the configured OpenRouter, OpenCode Go, OpenCode Zen, or LM Studio endpoint. OpenCode Go and OpenCode Zen use separate API keys. Whether LM Studio is local or remote depends on its configured URL.

Switching a task to another agent can send earlier conversation messages, tool inputs and outputs, file changes, images, and compaction summaries to that agent’s provider. Review the transfer preview before sending the message that applies the switch. Thinking and encrypted reasoning are excluded.

When Smart routing runs, Cockpit sends the task instruction, routing policy, available agents, models and reasoning levels, and each candidate workspace's display name, local path, and kind to the authenticated AGI Backend to select a combination. File and attachment content is not part of the routing request. If the instruction is empty and the task contains only attachments, their file names are sent in place of the instruction. Turn off Smart routing and choose the workspace and runtime settings manually when local paths must not be sent externally.

Sites opened in the in-app browser receive normal browser traffic such as input, uploads, cookies, and WebAuthn. Remote Access transfers task, Ask, Autorun, Fleet, Hook, and account information needed by the connected PWA or supported remote CLI command. A remote Hook command can register and execute shell code on the target computer.

The PWA Cockpit Browser view sends an authenticated Remote Access client a viewport image and limited metadata for a host tab. Every request verifies the task, assigned Browser Identity, session, and tab; it does not expose general browser RPC, storage, or the complete URL. Frames are JPEG images limited to 1280 pixels and 256 KiB and are not saved to host disk or PWA browser storage. PWA scroll controls change the host page's position but cannot activate links, type, or submit forms.

The PWA App Surface view sends an authenticated Remote Access client only the target metadata and latest host-held frame for the session attached to that task. Each request verifies the task-to-session relationship and does not trigger a capture, input, or Desktop panel change. A frame is re-encoded as a JPEG limited to 1280 pixels and 256 KiB, retained only as the mounted view's current in-memory image, and not written to host disk or PWA storage.

The PWA Skills view sends an authenticated client the project and global skills discovered by the host for that task, their definition metadata, and `SKILL.md` bodies. A request cannot supply an arbitrary directory: the host derives the search roots from the task and reads only definitions admitted by that task's skill discovery. A body is limited to the first 256 KiB and the surface is read-only. Protect paired devices on the assumption that they can read sensitive content stored in an available skill definition.

Enabling a Discord or Slack relay under **Settings → Ask notifications** sends the Ask summary, questions, choices, task name, a link back to Cockpit, and optional Ask images or videos to the selected channel. Members who can read that channel can see the post, but Cockpit accepts an answer only from the single configured `allowedUserId`. Relays are off by default and make outbound connections from Cockpit to the Discord Gateway or Slack Socket Mode.

## Anonymous data in guest use

Packaged guest use sends a random installation ID, app version, a daily session check, and onboarding events for steps reached, abandonment, guest selection, and sign-in to AGI Backend. Task instructions, conversations, file content, and project names are not included in these anonymous events.

An onboarding abandonment event that could not be sent is kept temporarily in the OS application-data area for retry. Anonymous events are sent only for guest use of a packaged app. Signed-in use sends the app version during authentication session checks.

## Distinguish approvals from Ask

`supervised`, `accept-edits`, and `full-access` define the boundary of agent tool operations. When one-time approval, always allow, or deny is offered, inspect the operation, path, command, and external destination. “Always allow” affects later operations of the same kind, so prefer one-time approval when the scope is unclear.

Ask returns a human policy decision; it is not a tool approval. Choosing “publish” in an Ask does not automatically grant permissions required by the operating system, an external service, or another tool.

The CLI can also decide pending tool approvals. Inspect `turnRequests` with `cockpit task get <id>` before using `task approve` or `task deny`; `approve` defaults to one-time permission, while `--scope always` remembers the decision. A decision made by another agent task is attributed to that agent, not to the human operator. `task answer` answers a runtime question; `cockpit ask answer` answers an Ask.

`task create --approval-mode <mode>` and `task approval-mode <id> <mode>` set permission boundaries for that task alone. Without a mode, the latter only reads the current setting. Supported modes are `supervised`, `accept-edits`, and `full-access`. Terminal UI tasks reject these operations. Protect CLI access as authority to change task permissions; a confirmation flag is not evidence of human authorization. See the [task CLI reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/task) for the supported agents and commands.

`cockpit task clear <id> --confirm` discards the conversation and hides its original instruction in all open desktop and mobile views. Drafts in other views are preserved. Clearing and compacting refuse a turn that is running or waiting for an approval or question. `task cancel` stops the current turn while keeping the task. Verify the exact task and preserve needed history before clearing it.

MCP tool confirmations in visual tasks always grant one-time approval, including when the CLI receives `--scope always`. MCP forms are runtime questions: answer them with `task answer`, or decline them in the desktop or PWA question card. `task deny` only selects approvals.

## Run Cockpit Hooks safely

Cockpit Hooks automatically run a registered shell action with the user's local permissions. They are separate from an agent's approval mode and do not sandbox the hook action. Register only scripts that you understand and control, then test them explicitly with `cockpit hooks test` before enabling them. `cockpit hooks` accepts `--host` for remote registration and execution. Remote Hooks authenticate like remote tasks: a computer on the same tailnet with the same Tailscale owner needs no token, and other connections need a paired bearer token. Either one authorizes registering and running arbitrary shell code with that computer’s user permissions, so protect your Tailscale devices and the token as control of the target instance. Script paths refer to the target computer.

The complete event JSON is delivered on standard input. Some values, including task names, working directories, Ask summaries, and selected text, are also available through environment variables. A hook that invokes an external command or network service can transmit those values. Select only the events and filters you need, and do not write tokens, personal data, or local paths to standard output or standard error. Their final 4 KB is stored in run history and can be inspected from Settings or the CLI.

Settings can disable or remove a hook, but removing it leaves existing run history in place. History rotates at 2,000 lines or 2 MB and keeps the latest 1,000 lines. Capturing selected text from a global hotkey on macOS requires Accessibility permission. Without it, the hook still runs with an empty selection value. Concurrency limits, timeouts, depth limits, and the circuit breaker reduce runaway execution; they do not make a registered command safe.

## Store credentials

AGI Cockpit tokens and API keys are stored in encrypted storage such as the OS Keychain or keyring. The Discord bot token and Slack bot and app-level tokens use this same boundary and are not returned as ordinary setting values or in status output. If secure storage is unavailable, Cockpit refuses to save rather than falling back to plaintext. Credentials that fail to delete are disabled and scheduled for deletion again on the next launch.

Named agent profiles isolate authentication. Antigravity keeps conversations, logs, cache, and usage history under a profile-specific home. On macOS, each profile stores its OAuth token in a dedicated Keychain, and Cockpit verifies that Keychain is first in the search order before starting a task. The host login Keychain remains later in the list for tools such as GitHub CLI, but Antigravity resolves the profile Keychain placeholder or token first. On Windows and Linux, the OS keyring itself is shared, so its host login is used by every profile. To rely on separate profile token files there, first log the default account out of the shared login.

Antigravity `accounts logout` previews every target and shared impact without `--confirm`. Running it for a named profile never removes the host login; when that profile uses shared authentication, the result points to the default-account logout instead. Logging out the default can also affect other profiles, ordinary terminal-launched Agy processes, and Gemini CLI authentication under the same home. Cockpit never logs an account out automatically. Browser Identities remain separate from agent account profiles.

Switching an active Claude, Codex, Grok Build, Antigravity, Cursor, or Qoder task copies its saved conversation into the selected account profile. When different target history would be replaced, Cockpit archives it first. Use separate tasks instead when policy requires conversation content never to cross profile boundaries.

The CLI can set the OpenRouter, OpenCode Go, OpenCode Zen, and Anthropic API keys with `cockpit settings set agents.credential.<name> --stdin` or `--key-file`. Never put a key in a command argument or task message. Reads report only whether a key is set. `settings reset agents.credential.<name>` removes that key from the same encrypted store used by Settings. These commands operate locally; protect local CLI access as authority to replace or remove provider credentials. CLI request bodies are passed without temporary request-body files. Internal credentials such as Cockpit's local tokens and authentication headers are not placed in child-process command-line arguments; the CLI passes them through standard input or a short-lived temporary file. For the latter, only the temporary path appears in the child arguments, and the file is removed after handoff.

## Enter a password once

When an agent uses `cockpit secret request`, you can deliver a value once to a specified browser password field from the dedicated Desktop window and Ask tab, or from the **Inbox** in an HTTPS-connected PWA. The dedicated window follows the Ask-window setting, but this remains separate from an Ask answer. Review the PC, task, purpose, Browser Identity, actual URL, destination, and deadline, then select **Fill this field once**. It does not click a login button or submit the form automatically.

The destination must be a unique, visible, enabled password field in the main frame on HTTPS or loopback HTTP. Changing the page or field after the request causes delivery to fail; Cockpit does not choose another field. Requests expire within ten minutes. PWA submission and cancellation require authenticated HTTPS/WSS and never fall back to an unencrypted connection. The CLI cannot accept or retrieve the value.

Delivery values stay in transient memory and are not saved in Cockpit conversations, Ask answers, CLI results, diagnostic logs, persisted tasks, PWA storage, or Ask forwarding. They are not restored or resent after restart. An unconfirmed delivery is not automatically retried; check its status instead. Cancellation applies only before delivery starts. When a request completes, fails, is cancelled, or expires, its input surface disappears immediately everywhere and leaves no result on screen. The requester can inspect the result with `cockpit secret status` or the value-free notification sent to its task.

The destination site can retain the value, and tools that read its DOM or evaluate scripts may read it after input. This feature does not guarantee that AI cannot read destination data. Discarding the delivery copy does not clear the site's field. Use trusted PCs, PWA devices, and sites. See the [secure input CLI reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/secret) for details.

## Isolate Browser Identities

The in-app browser grants site permissions such as notifications, geolocation, and clipboard reading and writing by default, without a Cockpit confirmation dialog. OS and Web API restrictions still apply. It denies media capture including camera and microphone, screen capture, fullscreen, automatic fullscreen, permission to open external apps, keyboard lock, and deprecated synchronous clipboard reading. Open only sites you trust. This policy applies to every Browser Identity; separating Identities does not restrict site permissions.

Each Browser Identity persists its own cookies, cache, localStorage, permissions, proxy authentication, and browser sessions. A task can be assigned several Identities, with one primary Identity; each session belongs to exactly one Identity. Agent commands can access only assigned Identities. Autoruns assign an initial Identity to new tasks, and Default is used when none is selected.

On macOS, `import-session` imports cookies belonging to the visible site's registrable domain and localStorage for the exact origin from Chrome into the selected Identity. It does not import sessionStorage, IndexedDB, extension state, device-bound authentication, or passkeys themselves.

On macOS, source selection also supports Brave, Edge, Arc, Vivaldi, Opera, and Firefox. Use `cockpit browser import-sources` to inspect sources and `--browser` with `--profile-id` to select one explicitly. Without a source selection, Cockpit uses the most recently used preferred profile among detected browsers. Chromium sources can import cookies and exact-origin localStorage; Firefox imports cookies only and reports that limitation. Each Chromium browser uses its own Safe Storage Keychain item, while Firefox cookie import does not access Keychain.

Clearing an Identity closes its sessions. Removing it also deletes persistent data. An Identity referenced by a running task or Autorun cannot be removed without a replacement, and the Default Identity cannot be removed.

See [Browser Identity](https://agi-labo.com/en/tools/cockpit/docs/browser-identities) for assignment and removal procedures.

Importing a browser sign-in copies the source browser’s session; it does not sign in with a different account. Check the source account before importing it into an Identity.

## Handle attachments and Ask media

An HTML explanation attached to an Ask is stored with the unanswered Ask and deleted when it is answered or closed. Desktop and the PWA render it in an isolated, display-only area without scripts, forms, or Cockpit actions. HTTP/HTTPS links open in the system browser. Discord and Slack relays do not include the HTML body, so information needed to decide must also appear in the summary or choices.

Local image previews in PWA conversations are sent from the host as resized JPEGs over an authenticated Remote Access connection. Only absolute paths recorded in the target task’s persisted image_view results are allowed; this does not provide access to arbitrary local files. Source size and pixel count are checked as well.

Attachments are stored unchanged under randomized names in a Cockpit-managed area. Archives, executables, and unknown extensions are accepted; Cockpit does not reject extensions or verify that content matches the declared MIME type or file format. Actual size and file-count limits are enforced. One message accepts up to eight files, 512 MB each and 1 GB total; JSON is limited to 25 MB. Acceptance does not establish that a file is safe.

Antigravity Native UI handles image attachments from Desktop and the PWA directly. When an image is outside the workspace, Cockpit copies it under `.agi-cockpit-attachments` in the canonical workspace, using a random per-session directory whose contents are excluded by `.gitignore`. An image already inside the workspace is not copied. Cockpit refuses to follow a symlink or another non-directory staging root, removes that session's copies when the session, CLI, or app stops, and removes unowned directories older than 24 hours when another session starts in the same workspace.

This staging does not expand Antigravity's `supervised` boundary to read arbitrary files outside the workspace. It explicitly prepares an image inside the workspace before Antigravity reads it. A sensitive image therefore exists temporarily inside the working project, and the turn fails if Cockpit cannot write the staging copy there.

When the relay option to attach files posted in Discord or Slack is enabled, Cockpit downloads files posted by the allowed user in the configured channel into managed storage and attaches them to the next response for the matched Ask. A reply targets that Ask; otherwise the file is assigned to the newest unanswered Ask in the same channel. Disable this option when it is unnecessary, and do not post sensitive files to a shared channel.

File names and content are not trusted instructions. Desktop chat offers a preview action only for a recognized local file link, and previewing never executes the file. Cockpit checks a Windows drive path first, but does not contact a UNC path while the conversation renders; its first read happens only after you select the link. A supported Windows `file:` URL is normalized into a local or UNC preview path. POSIX `file:` URLs, unsupported URL schemes, and data URLs that could contain executable content are not opened. Remove personal information, local paths, tokens, and session data before external sharing.

Desktop video preview omits the file path from its URL and reads only the needed ranges through an unguessable temporary URL that belongs to the renderer showing the preview. Closing the preview, navigating its main page, or destroying the renderer revokes that access. Each read verifies that the target is still the same regular file, so reusing the URL cannot switch it to another file or a symbolic link.

Fleet command gates save complete stdout and stderr per attempt under the Run's `fleet-runs/<runId>/gates/` directory, retaining only the head and tail when a log exceeds 20 MB. Output may contain tokens, local paths, test fixtures, or personal data. Inspect it before sharing from the Fleet panel or `cockpit fleet output`. Before removing an unneeded terminal Run, preserve only the diagnostic evidence that is still required in a safe location.

Local `task create` and `task send` can attach files through repeatable `--media` arguments, using the same managed upload store and limits as the GUI. The source files are copied, never moved or deleted; the attachments can be sent to the selected agent provider. Remote `--host` delivery is not supported.

## Protect Remote Access

When the Desktop device filter displays another PC, it receives task-list metadata from a PC authenticated as the same Tailscale owner, including task names, states, agents, working paths, instruction previews, timestamps, and whether an Ask is pending. This dedicated list connection does not synchronize conversation bodies or attachments and cannot control tasks. Received lists are cleared when the connection drops.

Task notifications, full state synchronization, and recent folder lists are sent only to authenticated sync connections. An unpaired connection is closed after two minutes. The PWA keeps the pairing form and entered code while reconnecting, and resends a submitted code if the connection drops before the authentication reply.

The recommended configuration is Tailscale-only access over HTTPS. Cockpit authenticates devices with Tailscale device information and a six-digit pairing code, with expiration and failure limits. It does not silently downgrade to HTTP when an HTTPS certificate cannot be obtained.

Only while HTTPS Remote Access is running, Cockpit checks the certificate at startup and every 24 hours and renews it through Tailscale when 30 days or less remain. Existing connections stay open, and the renewed certificate applies to new connections. Failure retries after six hours and produces a reason-specific warning when expiry is within 14 days or has passed. Renewal history does not store certificate contents, private keys, command output, or personal account details.

Remote CLI control accepts a computer on the same tailnet with the same Tailscale owner without a token. Other connections need the paired bearer token, and in Tailscale-only mode the target refuses connections that are neither a Tailscale peer nor loopback. Protect the token as control of the target Cockpit. Remote file paths refer to the target computer, and the transport does not fall back to local file IPC.

Local Wi-Fi mode is unencrypted and activates only after explicit confirmation. Do not use it on public or untrusted networks. Stopping Remote Access ends connected sessions and saves standalone state.

See [Remote Access](https://agi-labo.com/en/tools/cockpit/docs/remote-access) for setup details.

## Protect App Surface

App Surface attaches one target exclusively to one task. The first attachment to an Android physical device requires Ask approval, and `fill` refuses secure fields. The last frame may remain after disconnection, but the stale Surface disables interaction. The PWA can view this frame but cannot attach, detach, reconnect, or send input.

Cockpit does not start, stop, or install the target app. Completing or deleting a task detaches the target without closing its app.

See [App Surface](https://agi-labo.com/en/tools/cockpit/docs/app-surface) for attachment and operation procedures.

## Confirm completion and deletion

Completing a temporary-directory task deletes its workspace. Completing a Git Worktree task in Desktop preserves its Worktree, while CLI `task complete` removes it by default. Deleting tasks, bulk-deleting a Fleet Run and its tasks, or removing an Identity can destroy history and local data.

Before deletion, confirm the target ID, path, Git state, required artifacts, and recovery method. Obtain user approval immediately before publication, external transmission, purchase, or permission changes.

The Fleet Run menu and `cockpit fleet complete-tasks <runId>` complete remaining tasks in a completed, failed, stopped, or paused Run. Running Runs are refused; active tasks and sessions an unfinished loop will reuse are skipped. Use `--dry-run` to preview the targets. This keeps the Run and task history, and preserves Git Worktrees, but completing temporary-directory tasks deletes their workspaces. Preserve needed files first.

## PWA file access and HTML Surfaces

The paired PWA can browse and preview files below the selected task’s working folder. This is read-only access, including hidden files when requested; keep secrets out of folders shared through Remote Access. Build, dependency, and Git folders are excluded. Symbolic links are not followed, even when they point back inside the task folder. Text previews stop at 1 MB, images at 10 MB, and PDF, audio, and video at 50 MB. Background process views read only log files recorded for that task and cannot stop or restart a process.

HTML Surfaces block scripts by default on Desktop and the PWA. A Surface containing `<meta name="cockpit-scripts" content="isolated">` can run inline JavaScript for local calculations and display changes inside an isolated frame. It cannot access Cockpit, Node, local files, authentication, cookies, or app storage. Network requests, external resources, embedded pages, navigation, downloads, and popups are blocked. Inline event attributes and JavaScript URLs remain blocked. Display notices never run scripts.

Only a genuine user click on a declared action or validated submit control can send action and field values to the owning task. Script-generated clicks and messages cannot send, and `--non-interactive` disables sending while allowing local calculations. Reopening or replacing a Surface starts a fresh script context. See the [HTML reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/html) for limits and recovery. HTML file previews are separate: their sandbox can run scripts but does not grant access to the PWA, its storage, or its parent page.
