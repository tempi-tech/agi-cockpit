<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Approval modes and permissions

Learn the four approval modes for a Crew and how to choose one, what waiting for approval looks like and how to answer, and how child task approval modes are decided.

> Verified with AGI Cockpit 4.132.0 on 2026-11-13. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/crew-approval)

The approval mode decides at which of a Crew's operations it waits for a person's permission. **When a Crew stops replying, first check whether it is waiting for an approval.** This is where newcomers get stuck most often.

On this page, a "turn" is one round of work in which a Crew acts in response to one request or notice.

## What waiting for approval looks like

When a Crew waits for an approval, you see the following.

| Where | What you see |
| --- | --- |
| The Crew's conversation in Cockpit | A confirmation such as **Allow command execution?** or **Allow changing files?** appears |
| Desktop notification | When you are not looking at the Cockpit window, **Waiting for permission** appears. Only when the **Desktop notifications** setting is on |
| Slack thread | The working indicator changes to **Waiting for a confirmation** |
| Discord | The typing indicator stops while it waits for approval |
| Chatwork | There is no working indicator, so nothing changes in the chat |

When a child task waits for an approval, "“(task name)” is waiting for approval" appears in the Crew's conversation. When it is done, "“(task name)” got its approval answered" appears.

While it waits for an approval, the Crew's periodic checks do not run.

The **Waiting for approval** status for deciding whether to let someone who talked to the Crew through is a separate mechanism. See [People and places](https://agi-labo.com/en/tools/cockpit/docs/crew-people).

## Answer an approval

1. Open the Crew's conversation in Cockpit. For a child task's approval, use **Open** on the notice line to go to that task.
2. Read what the confirmation says.
3. Press **Allow** or **Deny**. **Always allow** may also appear.

When you are away, you can answer from the PWA through [Remote access](https://agi-labo.com/en/tools/cockpit/docs/remote-access) (AGI Lab members).

Some points trip people up.

- **You cannot approve from a chat.** Approval confirmations do not reach Discord, Slack, or Chatwork DMs either
- **While it waits for an approval, later requests wait too.** Until you allow it, neither that reply nor the next request moves forward
- **A Crew cannot answer its own approvals.** If it is waiting, only the owner can answer

### The owner's message cancels a pending approval

If the owner talks to the Crew in a chat while it is waiting for an approval, Cockpit denies the pending approval, cancels any question, and delivers that message to the Crew right away. The Crew is told what was cancelled, so it asks again if needed.

Messages from other people wait until the turn ends. Nobody other than the owner cancels an approval.

A request written in the Cockpit conversation waits until the turn ends. Pressing **Send now** on a waiting request first confirms "This cancels the approval the Crew is waiting for (and any question) and sends your message. Continue?", then cancels and delivers it the same way.

If it stops at the same place again and again, review the mode using the table below.

## The four modes

The further down the table, the more operations go ahead without confirmation.

| Mode | Behavior | When it suits |
| --- | --- | --- |
| Supervised | Confirms before running commands and changing files | When trying it out while watching what it does. When you are nearby |
| Auto-accept edits | Approves edits automatically and confirms everything else | When you leave edits to it but want to see commands |
| Auto | The agent's own review allows normal operations, and dangerous operations are stopped or confirmed | When you leave normal operations to it and want to check only dangerous ones |
| Full access | Allows commands and edits without confirmation | When stopping to wait would be a problem, and you can trust the people who may talk to it and what it reads |

Where it stops also differs by agent. For Codex and Antigravity, see [Differences between agents](#differences-between-agents) below.

Answering an Ask is separate. In every mode, a person answers an Ask.

## The starting mode for a new Crew

A new Crew starts out careful.

| Agent | Starting mode |
| --- | --- |
| Claude Code, Codex, Grok Build | Auto |
| Muse Code | Auto-accept edits |
| Other agents | Supervised |

You can change it on the New Task screen when creating the Crew.

## How to choose

**If you use a Crew from a chat while it is still Supervised, it stops often.** A Crew works when no one is watching, so its reply stops while it waits for approval.

**Auto may be unavailable or may not take effect.** Auto exists only for Claude, Codex, and Grok. Depending on the model or account, it cannot be used and the Crew runs Supervised. For Claude and Codex, a notice such as "Auto is not in effect in this session, so it is running in Supervised." then appears in the conversation. It never switches to Full access on its own.

**Decide on Full access together with who may talk to it.** In Full access, commands and file edits on this computer run without confirmation, triggered by requests from the people who may talk to it or by text the Crew reads (chat messages, attachments, web pages). See [Safety and treating people differently](https://agi-labo.com/en/tools/cockpit/docs/crew-safety).

## Change the mode

Change it with **Approval mode** in the input field of the Crew's conversation.

When the change takes effect depends on the agent.

| Agent | When the change takes effect |
| --- | --- |
| Claude | Immediately |
| Codex | From the next turn |
| Grok | Turning Auto on or off restarts the session. You cannot change it while it is working or waiting for an approval or a question, so try again after the turn ends |

When you change the agent, the current mode is kept if the new agent has it. Otherwise, the Crew uses that agent's most careful mode.

### Ask the Crew to change it

You can also ask the Crew to change it. The Crew can make a change that makes it more careful as it is.

**A change that loosens it needs the owner's approval.** Cockpit shows the confirmation "The Crew “(name)” asks you to approve a change to its own settings. Nothing changes until you approve it." Press **Approve** to make the change, or **Decline** to change nothing. Either way, the Crew is told the result.

Answer this confirmation on the Cockpit screen (on this computer, or in the Remote access PWA). It appears neither in a chat nor in an Ask DM.

Switching to an agent whose mode would be looser also counts as a loosening change. For example, switching a Supervised Crew to Muse Code makes it Auto-accept edits, so it needs the owner's approval.

## Child task modes

Child tasks a Crew hands work to follow these rules.

**A child task cannot be looser than its Crew.** If the Crew is Auto, a child task cannot be Full access. Asking for a looser mode lowers it to the Crew's mode. If the agent does not have that mode, it uses that agent's most careful mode. From strictest to loosest, the order is Supervised, Auto-accept edits, Auto, Full access.

### Two places that decide the mode

| Where | Role |
| --- | --- |
| **Default approval mode (Native UI)**, below **Default UI mode** on an agent's tab (such as Claude Code) under **Agents** in the app menu at the bottom left | A child task's **starting mode**. Set per agent. Used when the Crew does not specify a mode when creating a child task |
| **Approval mode** in the input field of the Crew's conversation | A child task's **upper limit**. It never becomes looser than this |

Combined, they work out as follows.

| Default approval mode | The Crew's mode | The child task's mode |
| --- | --- | --- |
| Full access | Auto | Lowered to Auto |
| Supervised | Auto | Supervised (the child task tends to stop for approvals) |
| Supervised | Full access | Supervised (only the Crew is fast; the child task tends to stop) |

**Even after you loosen the Crew, child tasks may stop.** Most often, **Default approval mode (Native UI)** is still Supervised.

### Other rules

- **Unless the Crew is in Full access, only the owner can allow a child task's approvals.** The Crew can only refuse them and answer questions
- **Unless the Crew is in Full access, it cannot run child tasks in a terminal.** The Terminal agent, the terminal UI, and custom commands that differ from the settings run outside the approval mode
- **Making the Crew more careful does not change existing child tasks.** It applies to child tasks created from now on and when their mode is changed
- The owner can make a child task looser than the limit in Cockpit
- If the Crew is in Full access, it can give a child task any mode

The same limit applies to Autorun tasks created by a Crew or its child tasks, and to Fleet nodes a Crew started.

## Differences between agents

Even for modes with the same name, where it stops differs by agent. The menu for choosing a mode also shows a description fitted to the agent.

**Codex:** Supervised, Auto-accept edits, and Auto run inside Codex's sandbox. The sandbox allows writing to the working folder and the project's additional folders (and on macOS and Linux, the temporary directory too), and blocks network access. So **even in Supervised, file edits inside the working folder and commands that stay inside the sandbox go ahead without confirmation.** It asks for approval for operations where Codex asks to run outside the sandbox, such as writing outside the working folder or network access. Auto-accept edits automatically allows the file-edit approvals among these. Full access does not use the sandbox and does not ask for approval.

With Codex on Windows, commands run inside the sandbox in every mode except Full access. Folders on the network (such as shared drives) are not writable locations, and even commands that only read may fail or wait for approval.

**Muse Code:** Only Auto-accept edits (the starting mode) and Full access. Muse Code does not confirm before editing files inside the workspace, so Cockpit does not offer Supervised.

**Antigravity (child tasks only):** It cannot ask for approval while running, so in Supervised it automatically denies tools that need approval. It is not suited to child tasks that need edits or commands.

For details, see [Agents and models](https://agi-labo.com/en/tools/cockpit/docs/agents-and-models).

## Related pages

- [Safety and treating people differently](https://agi-labo.com/en/tools/cockpit/docs/crew-safety)
- [How a Crew works](https://agi-labo.com/en/tools/cockpit/docs/crew-work)
- [People and places](https://agi-labo.com/en/tools/cockpit/docs/crew-people)
- [Troubleshooting](https://agi-labo.com/en/tools/cockpit/docs/crew-trouble)
