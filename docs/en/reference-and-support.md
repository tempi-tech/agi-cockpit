<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Reference and support

Troubleshoot task state, settings, shortcuts, storage, agent authentication, Remote Access, Browser Identities, and App Surface.

> Verified with AGI Cockpit 4.61.0 on 2026-08-27. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/reference-and-support)

Use this reference to read current state accurately and isolate a problem to a small boundary. First record the task, agent, target connection, operating system, and app version, then follow the relevant recovery path.

## Task states

| State | Meaning | What to check next |
| --- | --- | --- |
| `running` | The agent is running | Current turn, logs, and queue |
| `waiting_confirmation` | Waiting for input, approval, usage capacity, or resume | `waitingReason` and the on-screen guidance |
| `completed` | The task was moved to completed | Artifacts and Worktree preservation |
| `error` | Launch or runtime failed | `errorMessage` and diagnostics |

`turn_complete` means one response ended; it does not complete the whole task. `needsResume` is supplemental state indicating that an incomplete task lost its runtime process and needs a resume action.

Common `waitingReason` values include `turn_complete`, `permission`, `question`, `terminal_prompt`, `usage_limit`, `runtime_error`, `idle_timeout`, and `unknown`. Use `cockpit task get <id>` to inspect state and source from the CLI.

## Settings and shortcuts

The application menu at bottom left opens appearance, notifications, shortcuts, agents, setup, skills, Browser Identities, history, Remote Access, and Autorun. The CLI exposes supported settings and constraints through `cockpit settings list` and `cockpit settings describe`.

Primary shortcuts include Quick Task, task search, and the send key. The send key can be Enter or Cmd/Ctrl+Enter; Shift+Enter inserts a line break. Shortcut conflicts and OS registration failures are rejected before saving.

After changing a setting, inspect the returned saved value. Numeric settings can be clamped to an accepted range. Existing Autoruns retain their saved runtime configuration and are not automatically changed by new global defaults.

## Data locations

| Data | Primary location |
| --- | --- |
| Cockpit tasks, Autoruns, Fleets, templates, CLI, and logs | `~/.agi-tools/data/cockpit` |
| User-wide CLI launcher | `~/.agi-tools/bin` |
| Persistent workspaces | `~/.agi-tools/workspaces` |
| Project files and Git Worktrees | Location selected when creating the task |
| Electron authentication, attachments, and browser profiles | OS AGI Cockpit application-data area |
| Authentication tokens and API keys | OS Keychain or keyring |

`cockpit doctor` reports the connected instance and CLI runtime. Diagnostic logs can contain local paths, agent names, and session state, so inspect them before external sharing.

## The app does not update

Start with [Update AGI Cockpit](https://agi-labo.com/en/tools/cockpit/docs/updates) to check the method for the current OS and distribution, the last install result, and diagnostic logs. If the problem remains, include `capability`, `lastError`, and the last install result from `cockpit update status`, plus the relevant time from `~/.agi-tools/data/cockpit/logs/updater.jsonl`, in the report.

See the [`cockpit update` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/update) for the exact CLI contract and [Release history](https://agi-labo.com/en/tools/cockpit/docs/releases) for published changes.

## An agent is missing or does not launch

1. Open Setup from the application menu and check CLI detection and version.
2. Run `cockpit setup agent status <agent>` to inspect the command, installed version, and available update.
3. In Agent settings, check the launch command, UI mode, and account authentication state.
4. If native option discovery failed, restore the connection and authentication before selecting a fixed model or reasoning value.
5. Terminal UI cannot use native UI sign-in guidance, so complete authentication inside that CLI's terminal flow.

A usage limit puts the task in `waiting_confirmation` with `usage_limit`. If Auto cannot recover, add or choose another available profile, then send a new instruction.

## A task does not resume

Inspect `needsResume` and `waitingReason`, then use `cockpit task resume <id>` to restore a saved session. A Terminal task starts a new shell in the same directory, so its previous foreground process is not restored.

CLI connections are not forwarded automatically to another instance. Inspect `instance`, the runtime path, port listeners, and authentication results from `cockpit doctor`, then run the command against the correct Cockpit. Do not guess another destination and resend after `instance_mismatch`.

## Remote Access does not connect

1. Confirm Remote Access is running in Desktop.
2. In Tailscale-only mode, confirm both devices are authenticated in the same tailnet.
3. For HTTPS, enable MagicDNS and HTTPS Certificates, then obtain the certificate again.
4. Confirm the QR code or connection URL uses HTTPS.
5. Check for an expired pairing code or the 30-second lock after three failures.
6. Run `cockpit remote-access status --verbose` to isolate mode, scope, certificate, and membership state.

Do not switch casually to local Wi-Fi mode. Restore the certificate or Tailscale setup. See [Remote Access](https://agi-labo.com/en/tools/cockpit/docs/remote-access).

## Browser Identity cannot sign in

Confirm the Identity name and color assigned to the task. Signing in through another Identity does not transfer state to the current task. On macOS, open the target site in Chrome and use `cockpit browser import-session` to copy eligible cookies and localStorage into the selected Identity.

If passkeys fail, run `cockpit browser diagnostics` and inspect platform-authenticator state and the most recent WebAuthn attempt. On Linux, use a security key or password.

See [Browser Identity](https://agi-labo.com/en/tools/cockpit/docs/browser-identities) and [cockpit browser](https://agi-labo.com/en/tools/cockpit/docs/browser) for details.

## App Surface does not attach

1. Run `cockpit app doctor` to inspect ADB, Xcode, the control runtime, and current attachments.
2. Run `cockpit app targets` to confirm the target is online and not used by another task.
3. Boot an iOS Simulator in Xcode first.
4. Enable debugging for an Android physical device and approve it on the device.
5. After attachment, run `cockpit app status` and inspect mirror, accessibility, input, and keyboard health.

An `offline` or `stale` Surface keeps only the last frame and cannot accept input. Prepare the target with platform tools and reattach; Cockpit does not start or stop the target or its app.

See [App Surface](https://agi-labo.com/en/tools/cockpit/docs/app-surface) for details.

## Report a problem

Collect reproduction steps, expected and actual results, AGI Cockpit version, OS and architecture, selected agent and UI mode, and error codes. Include relevant portions of `cockpit doctor`, updater logs, or a diagnostic report when needed.

Remove tokens, cookies, pairing codes, personal names in local paths, and secret file content. Check [Release notes](https://agi-labo.com/en/tools/cockpit/docs/releases) for an already published fix, then report the problem in the AGI Cockpit GitHub Issues.
