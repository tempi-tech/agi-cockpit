<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# What is AGI Cockpit?

Learn where AGI Cockpit runs AI agents and how the task list and task details connect human decisions with result review.

> Verified with AGI Cockpit 4.116.0 on 2026-10-30. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs)

AGI Cockpit is a desktop app for handing work to multiple AI agents and supervising their progress, requests for decisions, and results in one place. Creating a task, reviewing its result, sending follow-up instructions, and completing it all stay within the same unit of work.

A project groups one or more folders under one name. Tasks are organized by project, while each task starts in one selected working folder and supported agents can also use the project's other folders.

## How work moves through Cockpit

1. Choose or create a project, select the task's working folder, and give it an objective.
2. Start the selected AI agent.
3. Use the task list to distinguish running, awaiting confirmation, completed, and failed work.
4. Open task details to review the conversation, confirmation requests, diffs, files, web pages, connected app screens, and other results.
5. Send another instruction, answer an Ask, resume the task, or complete it.

Cockpit does not move the work itself into the cloud. Agent processes and working files remain on the computer running Cockpit. Network access by an agent or another service follows that product's own configuration.

The in-app browser stores sign-in state and site data in the local persistent area owned by the Browser Identity assigned to a task or Autorun. Each Identity has a separate area. Tasks and Autoruns without an explicit assignment use the Default Identity, which preserves the existing browser sign-ins.

## Four product surfaces

| Surface | Role | Primary user |
| --- | --- | --- |
| Desktop | Main place to create, list, inspect, configure, and review tasks | People |
| Agent process | Executes instructions and returns conversation or changes to the task | AI agents |
| PWA | Reviews and operates tasks, Asks, and Autoruns from another device | People |
| `cockpit` CLI | Operates tasks, Asks, Autoruns, the browser, App Surface, and other Cockpit surfaces | AI agents and people |

Every local feature is free and unlimited, with no account required. Claude Code, Codex, and other agents require their own plans. An AGI Labo membership ($20/month or $200/year, with a 7-day free trial) adds three Cockpit features: Autorun, remote access through the PWA, and smart routing.

## Crews (alpha)

A Crew is an agent with a continuing role, memory, and its own home folder. It can work on requests itself or delegate to child tasks. Crews are in alpha: behavior and settings may change.

1. Open the creation screen and choose **Crew** instead of **Task**. Choose a project or the Crew's own folder and a supported agent. Terminal and Antigravity cannot run a Crew.
2. Describe its role, or let it ask you. Review the proposed name and `AGENTS.md` instructions before agreeing to save them.
3. Talk to it inside Cockpit. Discord is optional; open the Crew's settings to connect it. Create a Discord application, enter its bot token in Cockpit’s dedicated input screen, invite it to a server, and send it a DM to identify the owner. Cockpit tries to enable **Message Content Intent** automatically; if it cannot, enable it in the Developer Portal, save, and verify again. An agent can request this screen with `cockpit crew connect <crew> --platform discord --secure-input` and receives only the result. Verify the user ID before confirming ownership.
4. Review the allowed people and places before accepting requests. Unknown people wait for approval; earlier messages are not delivered after approval. Use the channel's mention, all-message, or off setting to choose where it listens.

Each turn uses the owner's AI account, including requests from other allowed people. Check that service's terms and tell participants how their messages are used. A careful approval mode can pause replies until the owner answers in Cockpit; **Full access** permits commands and edits without asking. The home folder and Crew CLI restrictions are not a sandbox. Read [Security and data](https://agi-labo.com/en/tools/cockpit/docs/security-and-data#crews-access-and-retained-data) before connecting other people.

**Periodic check** is on by default, every 30 minutes between 08:00 and 23:00 in the host computer's local time. Set what to check in the home's `HEARTBEAT.md`; settings allow 30–240 minutes in 30-minute steps and a daily time window. Checks skip busy, stopped, approval-waiting, queued, retrying, and usage-limited Crews and times outside that window. Checks can also be skipped while the Crew's declaration that nothing needs attention still holds. This is separate from [Autorun](https://agi-labo.com/en/tools/cockpit/docs/autorun), which sends a scheduled instruction.

A Discord message can reach a running Crew immediately when its agent supports interrupting the current turn; approval and question waits still hold delivery. New messages retain the least trusted authority of the interrupted turn and the incoming request. Adding a person fetches their Discord display name; check the user ID when deciding access, including for someone who first mentions the Crew in an allowed channel.

The Crew list’s **…** menu supports renaming and opening settings, and the list shows the connected Discord application’s name. Settings remember the last tab opened. From the CLI, `cockpit crew update <crew> --icon <image>` changes its icon; `--project`, `--directory`, or `--home` changes where it works. Changing that location starts a new session carrying the conversation. The owner’s authenticated tasks can inspect and answer the Crew’s approval waits and confirmation Asks, and post as the Crew with `cockpit crew post`. A post is sent immediately as that account; verify the destination and content first. `cockpit crew history <crew> --link <Discord-message-URL>` reads the linked message with nearby conversation when the Crew has access. See the [Crew CLI reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/crew) for exact options and limits.

Stopping preserves the Queue; starting delivers its messages. Removing a Crew deletes its settings and attempts to move its home to the Trash. See the [Crew CLI reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/crew) for connection setup, memory, restart, and removal details.

## Where to start

- Prepare the app: [Install AGI Cockpit](https://agi-labo.com/en/tools/cockpit/docs/getting-started)
- Prepare an agent and the Cockpit integration: [Initial setup](https://agi-labo.com/en/tools/cockpit/docs/initial-setup)
- Carry the first request through completion: [Your first task](https://agi-labo.com/en/tools/cockpit/docs/first-task)
- Keep multiple pieces of work distinct: [Task list](https://agi-labo.com/en/tools/cockpit/docs/tasks)
- Send follow-ups to selected work: [Task details](https://agi-labo.com/en/tools/cockpit/docs/task-details)
- Review diffs, files, and reports: [Results and tools](https://agi-labo.com/en/tools/cockpit/docs/results-and-tools)
- Operate a running mobile screen: [App Surface](https://agi-labo.com/en/tools/cockpit/docs/app-surface)
- Hand a decision from an agent to a person: [Ask](https://agi-labo.com/en/tools/cockpit/docs/ask)
- Delegate and supervise one task from the CLI: [Task management (CLI)](https://agi-labo.com/en/tools/cockpit/docs/task-management)
- Delegate decomposition and integration of a broad objective: [Master Agent](https://agi-labo.com/en/tools/cockpit/docs/master-agent)
- Reuse dependency-aware multi-agent workflows: [Fleet](https://agi-labo.com/en/tools/cockpit/docs/fleet)
- Discuss in one shared conversation with agents and people: [Talk Rooms](https://agi-labo.com/en/tools/cockpit/docs/talk-rooms)
- Start a new task or send an instruction to an existing task at a set time or interval: [Autorun](https://agi-labo.com/en/tools/cockpit/docs/autorun)
- Run actions on task completion or a hotkey: [Hooks](https://agi-labo.com/en/tools/cockpit/docs/hooks)
- Compare support across agents: [Agents and models](https://agi-labo.com/en/tools/cockpit/docs/agents-and-models)
- Use multiple agent accounts: [Accounts and Auto](https://agi-labo.com/en/tools/cockpit/docs/accounts)
- Set reusable roles and quality standards: [System prompts](https://agi-labo.com/en/tools/cockpit/docs/system-prompts)
- Supervise work from a phone or another computer: [Remote access](https://agi-labo.com/en/tools/cockpit/docs/remote-access)
- Create a 3D model or picture book: [Creative Studio](https://agi-labo.com/en/tools/cockpit/docs/creative-studio)
- Operate real web pages with a person and agent: [cockpit browser](https://agi-labo.com/en/tools/cockpit/docs/browser)
- Separate website sign-in state: [Browser Identity](https://agi-labo.com/en/tools/cockpit/docs/browser-identities)
- Operate Cockpit from an AI agent or script: [Cockpit CLI](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli)

## Free access and AGI Labo membership

| Access | Scope covered by these docs |
| --- | --- |
| Free access | Create a task in Desktop, run an agent, and review the result and history dashboard. Every local feature is free and unlimited |
| AGI Labo membership | Everything in free access, plus Autorun, PWA remote access, and smart routing |

A lock on a feature means that feature requires an active AGI Labo membership. Authentication for Claude Code, Codex, or another task agent is managed separately from your AGI Labo sign-in.

## Next step

Continue through [Install AGI Cockpit](https://agi-labo.com/en/tools/cockpit/docs/getting-started), [Initial setup](https://agi-labo.com/en/tools/cockpit/docs/initial-setup), and [Your first task](https://agi-labo.com/en/tools/cockpit/docs/first-task).
