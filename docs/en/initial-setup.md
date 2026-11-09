<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Initial setup

Check an AI agent, prepare the Cockpit skill and cockpit command, and reach the point where you can create your first task.

> Verified with AGI Cockpit 4.127.0 on 2026-11-08. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/initial-setup)

By the end of this guide, an agent and the Cockpit integration will be ready for your first task. If the flow did not open on first launch, use the app menu in the lower-left corner and select **Setup** → **First-run setup**.

## 1. Check an agent

Setup checks Claude Code, Codex CLI, Grok Build, Antigravity CLI, Cursor CLI, Qoder CLI, and Muse Code (macOS/Linux). It shows whether each CLI is installed and, for some agents, whether sign-in is ready. When a detected CLI is not yet usable, the screen states **Plan and sign-in required**. One ready agent is enough for setup to create an Assistant Crew at the end. You can also choose **Set up later** to continue setup without a ready agent; setup then creates no Assistant.

If Cockpit cannot find the agent you want, select **Install**. The row shows installation progress and, if it fails, a reason such as network trouble, missing commands, or insufficient write permission. Retry after correcting the cause, or choose **Open in a terminal** to inspect the installation. Cockpit Agent and Terminal do not require an external CLI.

## 2. Configure the Cockpit integration

Setup automatically installs or refreshes the Cockpit skill for detected external agents and makes the `cockpit` command available. It adds the command directory to shell configuration on macOS and Linux or to the user `PATH` on Windows.

On macOS and Linux, Cockpit adds or removes only its managed block. It preserves other shell settings, existing `PATH` lines, blank lines, and line endings. When the command directory is already present in a user-managed `PATH` entry, Cockpit does not rewrite the shell file.

This updates each agent's user-level skill directory and the applicable user-level path configuration. HTML Mode is optional and can be installed separately. If you add an agent later, open **Skills** from the app menu to review its status.

## 3. Sign in to the agent

An AI agent's plan and authentication are separate from AGI Cockpit's local features and AGI Lab sign-in. Obtain an eligible plan from the provider and sign in to that provider account for Claude Code, Codex, or another selected agent.

In the agent step, select **Sign in** for Claude Code or Codex. Codex opens browser sign-in on every supported OS; Claude Code does so on macOS and Linux. Complete authentication in the browser and Cockpit returns to the agent list automatically. If the browser did not open, copy the displayed URL. For Claude Code, paste the browser code into the setup screen if requested. On Windows, Claude Code uses the guided terminal sign-in: pass the theme choice, then choose **Claude account with subscription** at **Select login method**.

If sign-in fails, follow its displayed reason and select **Try again**. Claude Code also offers terminal sign-in for supported failures. You can cancel and return to setup without signing in.

If a Claude Code, Codex, or Grok Build Native UI task starts before authentication, task details shows sign-in guidance and retries the first instruction after authentication. Terminal UI and Terminal do not provide this retry flow; complete the CLI sign-in inside the terminal, then resume or recreate the task.

For Muse Code, open **Settings → Agents → Muse Code → Authentication**. Choose **Muse Code login** and **Sign in to Muse Code** to run `muse login` in a terminal, or choose **Meta API key** and save your key. API-key mode cannot start tasks until a key is saved. After authentication, reload models and choose Native UI or terminal UI. See [Agents and models](https://agi-labo.com/en/tools/cockpit/docs/agents-and-models) for defaults and permission limits.

## 4. Choose how to use Cockpit

For local features only, select **Start without signing in** on the **Sign in with AGI Lab** screen. Sign in as an AGI Lab member if you want Smart routing, Autorun, or remote access from another device.

## 5. Continue recent work or skip

The **Continue where you left off** step lists up to 200 Claude Code and Codex sessions updated in the last 30 days, grouped by workspace. Select individual sessions or an entire workspace, then choose **Import selected**. Imported tasks resume in Terminal UI. You can also create a project for a listed workspace before importing, or choose **Skip for now**.

Importing reads the original session files locally without changing or deleting them, and it does not start an agent.

## 6. Start talking with your Assistant Crew

If you have no Crew yet, **Continue** on the last step makes Cockpit create one Assistant Crew. It is named **Assistant** in English and helps with research, summaries, drafts, and notes. It runs on the agent you made ready in the agent step, and on Claude Code when both Claude Code and Codex are ready.

Setup then finishes and the Assistant's conversation opens on the Crew screen. At the top of the conversation, the first instructions Cockpit sent the Assistant appear as one line, **First instructions**; select it to read them. The Assistant briefly introduces itself, gives three examples of what you can ask, and asks what you would like it to do. Its first reply is an AI response, so it takes from a few seconds to a few tens of seconds.

When you send a request, the Assistant does that job first and shows the result. It then confirms a role and a name that fit the job with an Ask. **Use this role** writes the role down and changes the name; **Keep as Assistant** leaves both as they are. Either way, it proposes one job to keep doing: an Autorun for AGI Lab members, and a periodic check for everyone else. If you close the conversation without talking to the Assistant, it stays with its fixed name and role, and you can talk with it from the Crew list.

When you already have a Crew, or you chose **Set up later** without a ready agent, setup finishes without creating the Assistant. When the Assistant cannot be set up, the reason is shown. **Retry** tries again, reusing the Assistant if it was already created. **Not now** finishes setup and opens the Assistant's conversation if it was already created.

One Crew is free, including stopped Crews, and the Assistant is that Crew; an AGI Lab membership is required to create more. To start with a task instead, continue to [Your first task](https://agi-labo.com/en/tools/cockpit/docs/first-task).

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
