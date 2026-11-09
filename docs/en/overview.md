<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# What is AGI Cockpit?

Learn where AGI Cockpit runs AI agents and how the task list and task details connect human decisions with result review.

> Verified with AGI Cockpit 4.129.0 on 2026-11-10. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs)

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

Local features such as parallel task management, every supported agent, and Master Agent are free, with no task cap and no account required. One Crew is free; additional Crews require an AGI Lab membership. Claude Code, Codex, and other agents require their own plans. An AGI Lab membership ($20/month or $200/year, with a 7-day free trial) adds three Cockpit features: Autorun, remote access through the PWA, and smart routing. It also removes the limit on Crews.

## Crews (alpha)

A Crew is an agent with a continuing role, memory, and its own home folder. It can work on requests itself or delegate to child tasks. Crews are in alpha: behavior and settings may change.

1. Open the creation screen and choose **Crew** instead of **Task**. Choose a project or the Crew's own folder and a supported agent. Terminal and Antigravity cannot run a Crew.
2. Describe its role, or let it ask you. Review the proposed name and `AGENTS.md` instructions before agreeing to save them.
3. Talk to it inside Cockpit. Discord and Slack are optional, and one Crew can connect to both; open the Crew's settings to connect it. Create a Discord application, enter its bot token in Cockpit’s dedicated input screen, invite it to a server, and send it a DM to identify the owner. Cockpit tries to enable **Message Content Intent** automatically; if it cannot, enable it in the Developer Portal, save, and verify again. An agent can request this screen with `cockpit crew connect <crew> --platform discord --secure-input` and receives only the result. Verify the user ID before confirming ownership.
4. Review the allowed people and places before accepting requests. A new person’s DM or mention waits for approval, with ⏳ on the first request. Approval delivers that request after rechecking permissions, place, and attachment availability; other messages sent while waiting are not delivered. If it cannot be delivered, for example because the Crew is stopped, ask the person to send it again after approval. Use the channel's mention, all-message, or off setting to choose where it listens.

Each turn uses the owner's AI account, including requests from other allowed people. Check that service's terms and tell participants how their messages are used. A careful approval mode can pause replies until the owner answers in Cockpit; **Full access** permits commands and edits without asking. The home folder and Crew CLI restrictions are not a sandbox. Read [Security and data](https://agi-labo.com/en/tools/cockpit/docs/security-and-data#crews-access-and-retained-data) before connecting other people.

**Periodic check** is on by default, after 30 minutes without conversation activity, between 08:00 and 23:00 in the host computer's local time. Message receipt or delivery, response completion, and chat posts restart the interval: a response ending at 10:22 makes the next check due no earlier than 10:52. Internal notices and progress do not postpone it. Set what to check in the home's `HEARTBEAT.md`; settings allow 30–240 minutes in 30-minute steps and a daily time window. Checks skip busy, stopped, approval-waiting, queued, retrying, and usage-limited Crews and times outside that window. Checks can also be skipped while the Crew's declaration that nothing needs attention still holds. This is separate from [Autorun](https://agi-labo.com/en/tools/cockpit/docs/autorun), which sends a scheduled instruction.

A Discord message can reach a running Crew immediately when its agent supports interrupting the current turn; approval and question waits still hold delivery. New messages retain the least trusted authority of the interrupted turn and the incoming request. Adding a person fetches their Discord display name; check the user ID when deciding access, including for someone who first mentions the Crew in an allowed channel.

The Crew list’s **…** menu supports renaming and opening settings, and the list shows the connected Discord application’s name. Settings remember the last tab opened. From the CLI, `cockpit crew update <crew> --icon <image>` changes its icon; `--project`, `--directory`, or `--home` changes where it works. Changing that location starts a new session carrying the conversation. The owner’s authenticated tasks can inspect and answer the Crew’s approval waits and confirmation Asks, and post as the Crew with `cockpit crew post`. A post is sent immediately as that account; verify the destination and content first. `cockpit crew history <crew> --link <Discord-message-URL>` reads the linked message with nearby conversation when the Crew has access. See the [Crew CLI reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/crew) for exact options and limits.

A Crew's own session can inspect other Crews' settings, instructions, people, pending requests, and missions during automatic turns too. Strict mode refuses this in turns involving a member. Other Crews' chat history and credentials remain unavailable.

### Connect to Slack

On Desktop, choose Slack in the Crew’s connection settings and select **Connect to Slack**. Cockpit prepares the official Slack CLI in its own folder. Send the displayed line in a Slack channel or your own DM, allow the request in Slack, and paste Slack’s code into Cockpit. The line expires in about a minute; get a new one if it expires. Your workspace may require an administrator to approve the app. The PWA shows progress, but setup takes place on the computer.

You can also connect manually. Use **Create the app with these settings**, then get the Bot OAuth token (`xoxb-`) from **Install App** and the App-Level token (`xapp-`, with `connections:write`) from **Basic Information** of the same app. Setup includes buttons to open these pages. Enable Socket Mode and enter both tokens in the dedicated fields. Do not paste tokens into a conversation.

Finally, send the Crew a Slack DM and verify your user ID before confirming ownership. Invite it to channels with `/invite @name`. Mentions received before it joins can also reach it, subject to the same people and place permissions. On Discord, replying to the Crew’s own post also counts as a mention. The channel picker lists only channels the Crew can view; it hides channel names when Discord permissions cannot be checked.

### Choose when it responds to other people

Use **Active hours** in settings to choose days and hours. This uses the host’s clock and time zone; overnight hours belong to the day they start. Outside those hours, messages are skipped with 🌙 and are not delivered later. Owner messages and notices still arrive. This setting is separate from the periodic check’s hours.

### Discord profiles and posts

In settings, save **Profile description** and choose **Include the owner's name** to include an owner line. Discord shows this description when someone opens the Crew's icon. The limit is 400 characters, including the owner line. New Crews include the owner line by default; existing Crews start with it off and leave the bot's description untouched until you change the description or this setting.

Before its first successful write, Cockpit reads the Discord description again. If the field is empty, it imports the existing text without owner lines. If it finds a different description, it holds the write: review the displayed text, decide what to keep, and save. A failed read also prevents writing. After the first successful write, Cockpit's text takes precedence; an edit in Developer Portal lasts only until the next write. Settings shows connection errors and retry guidance.

On Desktop and the PWA, choose **Open in the app** or **Open on the web** on a post card to open its original Discord message. Discord posts can notify roles, `@everyone`, and `@here`. Grant the bot's role Discord's mention permission, or invite it again using the link in connection settings. Turns involving a member suppress mass notifications. See [Security and data](https://agi-labo.com/en/tools/cockpit/docs/security-and-data) for the authority boundaries.

Use `--interim` with `cockpit crew reply` or `post` to send progress while keeping the working indicator active. An ordinary reply stops that indicator. Discord receipt reactions are removed when the Crew replies or the turn ends.

### Slack profiles and rich posts

Cockpit can write **Profile description**, including the owner’s name, to Slack apps it created. Slack allows 139 UTF-8 bytes for the app description and 300 for the agent description; longer text is truncated. A Japanese character usually takes about three bytes. For a manually connected app, copy the text to **Short description** under **Basic Information** and the description under **Agents & AI Apps** in Slack; Cockpit cannot write it automatically.

Post cards on Desktop and the PWA can open the original Slack message. Slack posts can notify User Groups, `@here`, and `@channel`; turns involving a member suppress mass notifications.

Crews can post tables, charts, and cards to Discord and Slack. Supply structured content to `cockpit crew post` or `reply` with `--ui-file` or `--ui-stdin`. Conversion to images or messages differs by platform. See the [Crew reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/crew) for supported formats and limits.

### View the organization chart

AGI Lab members can open a Crew’s **Organization** tab on Desktop or the PWA to inspect people, Crews, other agents, roles, managers, and links. Choose an organization or device view and select a member to see details. The screen is read only. Ask a Crew to create, change, or delete entries, or use the CLI in the [org reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/org). Registration does not grant conversation or operating permissions. Review storage and personal-data limits in [Security and data](https://agi-labo.com/en/tools/cockpit/docs/security-and-data).

### Missions

Use a Mission to give a Crew a long-term goal (`goal`) or continuing responsibility (`ongoing`). Open the Crew's **Missions** tab, choose **New Mission**, describe the work, and select **Tell the Crew**. This sends a request to its conversation; answer its short questions about the objective, deadline, authority, and check-ins before it creates the Mission.

The tab shows **Now**, **Next step**, **Waiting for**, the deadline, recent activity, log, and **Needs you** Asks. The Crew keeps its brief in `missions/<id>/README.md` and advances active Missions through periodic checks. If periodic checks are off, it will not advance them by itself. Members can use Mission-linked Autoruns for check-ins at set times; without membership, check-ins use periodic checks and their approximate timing.

The owner can pause and resume a Mission, or mark it achieved or cancelled. A Crew's request to pause, resume, or end one waits for owner approval. Pausing stops linked Autoruns; resuming restores them. Ending stops linked Autoruns and closes outstanding Mission Asks. An ended Mission cannot be resumed; create another to continue. See the [Mission reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/crew-missions).

### Names, queue, and free limit

Crew names are limited to 32 characters. **Send now** on a queued message interrupts the current work when supported. If the Crew is waiting for an approval or question, Cockpit asks before cancelling that pending request and sending the message. Stopped Crews keep queued messages until started.

The free limit counts stopped Crews too. Existing Crews remain usable even if you already exceed the limit; it restricts new creation. Deleting a Crew frees its slot, but removes its settings and home as described below.

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

## Free access and AGI Lab membership

| Access | Scope covered by these docs |
| --- | --- |
| Free access | Create a task in Desktop, run an agent, and review the result and history dashboard. Local features are free with no task cap, including one Crew |
| AGI Lab membership | Everything in free access, plus Autorun, PWA remote access, smart routing, and no limit on Crews |

A lock on a feature means that feature requires an active AGI Lab membership. Authentication for Claude Code, Codex, or another task agent is managed separately from your AGI Lab sign-in.

## Next step

Continue through [Install AGI Cockpit](https://agi-labo.com/en/tools/cockpit/docs/getting-started), [Initial setup](https://agi-labo.com/en/tools/cockpit/docs/initial-setup), and [Your first task](https://agi-labo.com/en/tools/cockpit/docs/first-task).
