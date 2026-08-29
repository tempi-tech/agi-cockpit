<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Initial setup

Check an AI agent, prepare the Cockpit skill and cockpit command, and reach the point where you can create your first task.

> Verified with AGI Cockpit 4.61.0 on 2026-08-27. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/initial-setup)

By the end of this guide, an agent and the Cockpit integration will be ready for your first task. If the flow did not open on first launch, use the app menu in the lower-left corner and select **Setup** → **First-run setup**.

## 1. Check an agent

Setup checks Claude Code, Codex CLI, Grok Build, Antigravity CLI, Cursor CLI, and Qoder CLI. It shows whether each CLI is installed and, for some agents, whether sign-in is ready. One available agent is enough to continue.

If Cockpit cannot find the agent you want, select **Install**. Cockpit Agent and Terminal do not require an external CLI.

## 2. Configure the Cockpit integration

The integration step installs or refreshes the Cockpit skill for detected external agents and makes the `cockpit` command available. It adds the command directory to shell configuration on macOS and Linux or to the user `PATH` on Windows.

This updates each agent's user-level skill directory and the applicable user-level path configuration. HTML Mode is optional and can be installed separately. If you add an agent later, open **Skills** from the app menu to review its status.

## 3. Sign in to the agent

An AI agent's subscription and authentication are separate from AGI Labo sign-in. Make Claude Code, Codex, or another selected agent available through its own account.

If a Claude Code, Codex, or Grok Build Native UI task starts before authentication, task details shows sign-in guidance and retries the first instruction after authentication. Terminal UI and Terminal do not provide this retry flow; complete the CLI sign-in inside the terminal, then resume or recreate the task.

## 4. Choose how to use Cockpit

For local features only, select **Start without signing in** on the final screen. Sign in as an AGI Labo member if you want Autorun or remote access from another device.

Setup is complete when the **New task** screen opens and the agent you want is selectable. Continue to [Your first task](https://agi-labo.com/en/tools/cockpit/docs/first-task).

## If setup does not complete

| Message or state | What to check |
| --- | --- |
| The agent you want is missing | Select **Manage agents** to install its CLI or verify its launch command in Settings |
| **Verifying agents** appears | Cockpit is showing saved choices while rechecking them; wait for verification to finish |
| **Could not verify agents** appears | Review the saved choices and verify the CLI launch command under **Agents** in Settings |
| Native UI requires sign-in | Complete the guidance in task details and wait for the same instruction to retry |
| Terminal UI or Terminal requires sign-in | Complete that CLI's sign-in flow in the terminal, then resume or recreate the task |

After initial setup, use [Agents and models](https://agi-labo.com/en/tools/cockpit/docs/agents-and-models), [Accounts and Auto](https://agi-labo.com/en/tools/cockpit/docs/accounts), and [System prompts](https://agi-labo.com/en/tools/cockpit/docs/system-prompts) to tune the runtime environment.

## Related pages

- [Install AGI Cockpit](https://agi-labo.com/en/tools/cockpit/docs/getting-started)
- [Your first task](https://agi-labo.com/en/tools/cockpit/docs/first-task)
- [Agents and models](https://agi-labo.com/en/tools/cockpit/docs/agents-and-models)
- [Accounts and Auto](https://agi-labo.com/en/tools/cockpit/docs/accounts)
- [System prompts](https://agi-labo.com/en/tools/cockpit/docs/system-prompts)
- [`cockpit setup` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/setup)
- [Security and data](https://agi-labo.com/en/tools/cockpit/docs/security-and-data)
