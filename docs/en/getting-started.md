<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Install and complete initial setup

Install AGI Cockpit, prepare an agent and Cockpit integration, and reach the point where you can create your first task.

> Verified with AGI Cockpit 4.61.0 on 2026-08-27. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/getting-started)

By the end of this guide, you will be able to open AGI Cockpit, prepare an agent and Cockpit integration, and create your first task.

## 1. Install AGI Cockpit

Open the [AGI Cockpit download page](https://agi-labo.com/en/tools/cockpit) and choose the package for your operating system.

| OS | Package | Architecture |
| --- | --- | --- |
| Windows | Microsoft Store | x64 / arm64 |
| macOS | `.dmg` | Apple Silicon |
| Linux | AppImage / `.deb` | x64 / arm64 |

On Windows, install AGI Cockpit from Microsoft Store. On macOS, open the `.dmg` and move AGI Cockpit to Applications. On Linux, use the distributed AppImage or `.deb` package.

On a first run with no existing tasks, Cockpit opens a setup flow. It introduces the app, checks the supported task-agent CLIs, configures the Cockpit integration, then offers AGI Labo sign-in. One available agent is enough to continue. Choose **Start without signing in** at the final step if you want to use guest mode; sign in as an AGI Labo member if you need Autorun or remote access from another device. You can revisit the flow later by opening the app menu in the lower-left corner, then selecting **Setup** and **First-run setup**.

AGI Cockpit stores its own credentials and API keys in encrypted operating-system storage such as Keychain or a keyring. If encrypted storage is unavailable, Cockpit does not fall back to plaintext. It rejects the save and shows recovery guidance. Enable the operating-system Keychain or keyring, then sign in again. Named Antigravity accounts are an exception: their authentication is isolated in the profile-specific app-data area described below.

Remote access defaults to Tailscale-only mode. When Tailscale HTTPS is enabled but its certificate is unavailable, Cockpit does not silently downgrade to HTTP and does not start the connection. Local Wi-Fi mode is unencrypted, so Cockpit enables it only after an explicit confirmation and keeps a warning visible while it is active. Do not use local Wi-Fi mode on a public network. See [Remote access](https://agi-labo.com/en/tools/cockpit/docs/remote-access) for the setup steps.

## 2. Prepare an agent

The setup flow checks Claude Code, Codex CLI, Grok Build, Antigravity CLI, Cursor CLI, and Qoder CLI. It shows whether each CLI is installed and, for Claude Code, Codex CLI, and Grok Build, whether sign-in is ready. If Cockpit cannot find an agent, select **Install**.

The new-task screen shows only available agents. An agent that needs its own CLI appears only when Cockpit can detect that CLI. Cockpit Agent and Terminal do not require CLI detection. To add a missing agent, open Settings from **Manage agents**, then install its CLI or verify its launch command. Return to the new-task screen after installation; the agent is ready when it becomes selectable.

After the first CLI check finishes, Cockpit saves the result. On later visits, you can use the saved agent choices immediately while Cockpit rechecks their current state in the background. A **Verifying agents** or **Could not verify agents** icon beside the choices means the screen is showing the saved result. Only a first visit with no saved result keeps the choices in a loading state until the check finishes.

The integration step automatically installs or refreshes the `cockpit` skill for detected Claude Code, Codex CLI, Antigravity CLI, Cursor CLI, Qoder CLI, and Grok Build installations. It also installs the `cockpit` command and adds its directory to your shell configuration on macOS and Linux or to your user `PATH` on Windows. This writes the generated skill into each detected agent's user-level skill directory and updates the applicable user-level path configuration. HTML Mode is optional and remains a separate installation. Cockpit refreshes the core skill and command on later app starts; open **Skills** from the lower-left app menu to review their status after adding another agent.

When you use Claude Code, Codex, or Grok Build in Native UI, you can start before authenticating. Task details then shows sign-in guidance, and Cockpit retries the first instruction in the same task after authentication succeeds. This guidance and automatic retry do not apply to Terminal UI or the Terminal agent. In those modes, complete the CLI's sign-in flow inside the terminal, then resume or recreate the task.

An AI agent's subscription and authentication are separate from your AGI Labo sign-in. Signing in as an AGI Labo member does not authenticate Claude Code, Codex, or another task agent.

Under **Agents** in Settings, you can create account profiles for Claude Code, Codex, Grok Build, Antigravity, Cursor, and Qoder. Each profile isolates authentication and connection settings. New tasks, Autoruns, and Fleets default to **Auto**, which selects a signed-in account from its usage state. You can instead pin the default account or a specific profile and can switch accounts later on supported running tasks. Antigravity profiles use browser-based Google OAuth and store OAuth tokens, conversations, logs, and usage history in a profile-specific app-data area. Cockpit starts Agy with that selected area and prevents fallback to the shared keyring. Developer shell resources and non-credential Antigravity settings remain shared with the normal home directory. On macOS, isolated profiles can use the host user's Keychain search list, so GitHub CLI, Git credential helpers, and similar developer tools can reuse existing Keychain credentials. This access does not share Antigravity's profile-specific data.

Claude, Codex, and Qoder Native UI tasks and Cockpit Agent can select a custom system prompt in addition to their built-in choices. Register one with `cockpit system-prompt add`; it then appears in the system-prompt picker for new tasks and Autoruns in Desktop and the PWA.

```bash
cockpit system-prompt add reviewer --prompt "Review changes for correctness and clarity."
cockpit system-prompt list
```

The default `append` mode adds the content after Cockpit's standard system prompt and preserves its built-in Cockpit CLI knowledge. `--mode replace` replaces the standard prompt, leaving that knowledge available only through the installed Cockpit skill. Custom prompts are stored as user-owned Markdown in the AGI Tools data area and are not overwritten when Master Agent files are redeployed. Their content is sent to the selected agent, so do not include credentials or secrets. Cursor, Grok Build, Antigravity, Terminal, and terminal UI modes do not accept custom prompts.

## 3. Update AGI Cockpit

Check for a new version from the update notice in the lower-left corner or open **Updates** from the app menu in the same location. On platforms where Cockpit downloads the update, use the on-screen action to restart and install it when ready. On Linux, the notice opens the download page that matches the current display language.

When Cockpit requires a newer version before continuing, the screen shows release notes and a download destination in the app's current language. Review the Japanese guidance in the Japanese UI or the English guidance in the English UI before updating.

If an update check fails because of a temporary network or server problem, Cockpit retries it once automatically. If the check still fails, the Updates panel shows guidance for the failure and offers **Try again** and **Update manually**. If Cockpit cannot validate the update data, it does not continue with automatic installation; follow the manual-update guidance instead.

On packaged macOS builds, if the app has not quit within five minutes after an install request, Cockpit records the stalled handoff and asks the installer to try once more. Diagnostic events for update checks, downloads, verification, install requests, and restarts are saved in `~/.agi-tools/data/cockpit/logs/updater.jsonl`; their summary is also available from `cockpit update status` and the diagnostic export. If the current version still starts after the retry, inspect the last install result and open the distribution page through **Update manually**.

## 4. If setup does not complete

| Message or state | What to check |
| --- | --- |
| The agent you want is missing from the list | Select **Manage agents** to install its CLI or verify the launch command in Settings |
| Native UI requires sign-in | Complete the guidance in task details and wait for Cockpit to retry the same instruction |
| Terminal UI or Terminal requires sign-in | Complete that CLI's sign-in flow in the terminal, then resume or recreate the task |

## Related pages

- [Your first task](https://agi-labo.com/en/tools/cockpit/docs/first-task)
- [Task list](https://agi-labo.com/en/tools/cockpit/docs/tasks)
- [Remote access](https://agi-labo.com/en/tools/cockpit/docs/remote-access)
- [Ask](https://agi-labo.com/en/tools/cockpit/docs/ask)
- [Autorun](https://agi-labo.com/en/tools/cockpit/docs/autorun)
- [Release history](https://agi-labo.com/en/tools/cockpit/docs/releases)
