<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# What is a Crew

Learn what a Crew can and cannot do, how it differs from tasks and the Master Agent, which agents can run it, and pricing and the limit on Crews.

> Verified with AGI Cockpit 4.132.0 on 2026-11-13. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/crew-about)

A Crew is an AI agent that lives in AGI Cockpit. It has a role and memory, and it works on what you ask either by itself or by handing it to child tasks, then returns the result. You can talk with it inside Cockpit without connecting a chat. Connect it to Discord, Slack, or Chatwork (Chatwork is for members), and you can also talk with it there.

Crews are in alpha. How they work and their settings may change.

If you do not have a Crew yet, start with [Create your first Crew](https://agi-labo.com/en/tools/cockpit/docs/crew-first).

**Next, read [Approval modes and permissions](https://agi-labo.com/en/tools/cockpit/docs/crew-approval).** This is where newcomers get stuck most often. When a Crew stops replying, it is often waiting for an approval.

## How it differs from tasks and the Master Agent

| | Suited to | How long it continues |
| --- | --- | --- |
| Task | Asking for one clear result | Done when that job is finished |
| [Master Agent](https://agi-labo.com/en/tools/cockpit/docs/master-agent) | Breaking a goal down and coordinating who does what and in what order | While the goal is being pursued |
| Crew | Handing over a fixed role for the long run. You ask through conversation or chat | Keeps its role and memory, and continues across jobs |

Crews appear in the task list. Child tasks a Crew creates sit under it.

## What a Crew can do

- Take requests: in its Cockpit conversation, or in DMs and channels in Discord, Slack, or Chatwork (Chatwork is for members)
- Do the work: by itself, or by handing it to child tasks (Claude Code, Codex, and others) and seeing them through until the results come back
- Reply in a readable form: when comparing things, for example, it posts tables, charts, and cards to the chat
- Remember: it keeps its role and rules in `AGENTS.md`, and what it has learned and a record of its work in its home folder. See [Home and memory](https://agi-labo.com/en/tools/cockpit/docs/crew-home)
- Check on its own: it reviews its own business at a set interval. See [Periodic checks and Autoruns](https://agi-labo.com/en/tools/cockpit/docs/crew-schedule)
- Carry long jobs: hand ongoing work to it as a [Mission](https://agi-labo.com/en/tools/cockpit/docs/crew-missions), and you can follow the progress in the **Missions** tab
- Ask for decisions: when a decision is needed, it asks with an Ask. You can also receive and answer Asks in Discord, Slack, and Chatwork DMs. See [Asks and Crews](https://agi-labo.com/en/tools/cockpit/docs/crew-ask)

## Agents that can run a Crew

A Crew can run on Claude Code, Codex, Cursor, Qoder, Grok Build, Muse Code (macOS and Linux only), and Cockpit.

Terminal and Antigravity cannot run a Crew. Child tasks a Crew hands work to can also use Antigravity. For the differences between agents, see [Agents and models](https://agi-labo.com/en/tools/cockpit/docs/agents-and-models).

## What a Crew cannot do

| Common assumption | In fact |
| --- | --- |
| It replies at any time | A Crew runs on the owner's computer. It replies only while that computer is on and Cockpit is running. It stops while the computer sleeps or is turned off. On macOS and Linux, Cockpit can start automatically when you log in |
| It reads messages sent while it was stopped later | It does not go back for messages sent while the Crew was stopped or Cockpit was closed. Only in Chatwork do messages sent while Cockpit was closed arrive later |
| It runs fully on its own | Depending on the approval mode, it stops at each operation. When a decision is needed, it asks with an Ask |
| It connects to any chat | It connects only to these three: Discord, Slack, and Chatwork (Chatwork is for members). For how to connect, see [Connect a chat](https://agi-labo.com/en/tools/cockpit/docs/crew-chat) |
| People who may talk to it use it with their own accounts | Requests from other people also all run on the owner's AI account |
| Writing rules makes it safe | A Crew's rules are there to prevent mistakes; they are not a security boundary. See [Safety and treating people differently](https://agi-labo.com/en/tools/cockpit/docs/crew-safety) |

Some AI services forbid letting other people use your account. Whether to add others to the people who may talk to it is the owner's decision, after checking each service's terms.

## Pricing and the number of Crews

- AGI Cockpit's local features are free (up to 1 Crew). No account registration is needed
- The 1 Crew includes stopped Crews and Crews created by a Crew. Crews you already created keep working, however many there are. The limit only refuses creating a new one
- AGI Lab members have no limit on the number of Crews. Membership is $20/month or $200/year, with a 7-day free trial
- The cost of running the AI is covered by the owner's agent subscriptions or keys (Claude, ChatGPT, and others). It is separate from the membership fee

The following Crew-related features are also for AGI Lab members.

- [Autorun](https://agi-labo.com/en/tools/cockpit/docs/autorun): asks a Crew to do work at a set time
- [Remote access](https://agi-labo.com/en/tools/cockpit/docs/remote-access): use a Crew from a smartphone or another device. Also used to communicate with Crews on other computers
- [Connect to Chatwork](https://agi-labo.com/en/tools/cockpit/docs/crew-chat): lets a Crew talk in Chatwork

## How to ask

You can write requests to a Crew the way you would talk to a colleague. You can also start by asking it in conversation to change its settings.

- "Add what I want you to check during periodic checks to `HEARTBEAT.md`"
- "Tell me what you are waiting for right now"
- "Hand this research to a child task, and summarize the results when it finishes"

If something cannot be changed by the Crew itself, the Crew says so. In that case, the owner makes the change on the Cockpit screen.

## Related pages

- [Create your first Crew](https://agi-labo.com/en/tools/cockpit/docs/crew-first)
- [Approval modes and permissions](https://agi-labo.com/en/tools/cockpit/docs/crew-approval)
- [People and places](https://agi-labo.com/en/tools/cockpit/docs/crew-people)
- [How a Crew works](https://agi-labo.com/en/tools/cockpit/docs/crew-work)
- [Troubleshooting](https://agi-labo.com/en/tools/cockpit/docs/crew-trouble)
