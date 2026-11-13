<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Safety and treating people differently

Learn what the Crew rules can and cannot stop, two ways to treat people differently, the terms of AI services, and what to tell the people in the chat.

> Verified with AGI Cockpit 4.132.0 on 2026-11-13. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/crew-safety)

**The Crew rules are there to prevent mistakes. They are not a security boundary.**

Crews and their child tasks run as the same OS user as the owner. In general, anything the owner can touch, the Crew can touch too. Do not put secrets you do not want the Crew to see, or things you do not want broken, within the Crew's reach.

## What can and cannot be stopped

This section separates what Cockpit's mechanisms stop from what is only an instruction to the Crew.

### Stopped by mechanisms

| What is stopped | Mechanism |
| --- | --- |
| A message from someone who may not talk to it reaching the Crew | Messages from people not on the list are not delivered and go into **Waiting for approval**. This is decided by the sender ID that the chat reports |
| A message in a place that is off reaching the Crew | The place settings for where it responds do not deliver it |
| Pretending to be the owner through the text of a message | Cockpit attaches the "owner" mark based on the sender ID. It cannot be attached from the message text |
| Allowing an action waiting for approval through a chat message | Actions can be allowed only in Cockpit |
| Approving a change that widens what the Crew can do through chat | This approval can be answered only in Cockpit (including the PWA). It is not sent to Ask DMs either |
| A request to enter a secret appearing in the chat | The owner is only told by DM that something is waiting |
| Codex writing and network access outside the place it works | In every mode other than Full access, Codex runs with writing limited to the place it works and added folders, and with the network cut off |

### Stopped by rules that prevent mistakes

Crews and child tasks are stopped from accidentally doing the following through Cockpit. **Only mistakes made through Cockpit's defined routes are stopped.**

| What is stopped | Notes |
| --- | --- |
| A Crew or child task answering its own approval | A Crew cannot press **Allow** itself |
| A child task changing the Crew's settings or the people who may talk to it | It can make requests to the Crew |
| A Crew changing Crews other than itself and the Crews under it. A Crew deleting a Crew | The owner does these in Cockpit. For Crews under it, see [Multiple Crews](https://agi-labo.com/en/tools/cockpit/docs/crew-multiple) |
| Making the approval mode less cautious, or greatly widening who may talk to it | Waits for the owner's approval |
| Giving a child task a less cautious approval mode than the Crew | See [Approval modes and permissions](https://agi-labo.com/en/tools/cockpit/docs/crew-approval) |
| Reading DMs with other people, or outside the place it replies, in a turn not started by the owner | Refused with `history_not_allowed` |
| Making a change only the owner can ask for, at the request of people who may talk to it | Refused with `owner_request_required`. Nothing changes |

### Cannot be stopped

| What cannot be stopped | Reason |
| --- | --- |
| A Crew in Full access running commands or editing at the request of people who may talk to it | It is a mode that runs without asking |
| Being manipulated by text it reads | It is instructed to treat chat messages, attachments, web pages, and history as data, but it may not follow that |
| The rule to handle other people's requests carefully being broken | It is an instruction, not a guarantee. The same goes for rules in `AGENTS.md` |
| Touching files outside its home as the same OS user as the owner | The home is not a place that confines the Crew |
| Codex reading files outside the place it works | Cockpit runs Codex with settings that do not limit reading |
| Operating the owner's other tasks and Autoruns | It can complete, delete, run, turn on or off, and so on, the same as a task |
| Many operations on Hooks, settings, and remote access | A Crew can use them just like the owner. Hooks can run shell commands as the owner |
| Operating Cockpit through another route | A process running as the same OS user reaches outside the Crew rules |
| Keeping memory separate for each person | A Crew has one home and one memory. What it learned from one person's request may carry over to later turns through conversations or files |

Ask answers are protected by a separate mechanism. To answer an Ask as the owner, a verified task's credentials or the owner's pairing credentials are required.

## What to do to stay safe

1. **Limit the people who may talk to it.** The more people you add, the more entry points there are for manipulation
2. **When you use Full access, limit the people who may talk to it and what it reads to a range you trust**
3. **Do not keep secrets there.** Do not put `.env` files or keys in the Crew's home or the place it works
4. **Separate places it works.** Run it in a project away from anything important
5. **Protect your OS account.** As long as it runs as the same OS user, the Crew rules cannot fully separate things

## Treating people differently

There is no setting that changes permissions per person. There are two ways to treat people differently.

| | Write it in `AGENTS.md` | Split into separate Crews |
| --- | --- | --- |
| Effort | Little | More. Each also needs its own chat app |
| People who may talk to it | Same | Separate |
| Place it works and approval mode | Same | Separate |
| Session and memory | Same. Not separated per person | Separate |
| How it takes effect | As instructions. They may be broken | Separated by settings. However, the OS user is the same |
| Good for | Handling where a slip is not a problem, such as how to reply | When you really want to separate what information is shown or what is entrusted |

### Writing it in `AGENTS.md`

Chat messages reach the Crew with the sender's ID and role attached. The role is either "owner" or "allowed person." Cockpit attaches it, so it cannot be changed from the message text. The Crew sees this and tries to follow the per-person handling written in `AGENTS.md`.

**However, this is an instruction, not a guarantee.** Files and commands are not stopped this way. Anyone who can use the owner's chat account can talk as the owner. Protect the owner's account carefully.

### What you can decide on screen

Among the kinds of handling that differ by person, the following can be set in the settings.

| Setting | Choices | What it affects |
| --- | --- | --- |
| **Changes asked for in chat** | **The owner and allowed people**, **Only the owner** | Who can ask the Crew to change its own settings |
| **Interrupting its work** | **Everyone**, **Owners only** | Who can stop the Crew's current work and have their message delivered right away |
| **DM** under **Where it responds** | **Owners only** and others | Who can talk to it in a DM |

When **Changes asked for in chat** is **Only the owner**, a turn started by a request from people who may talk to it is treated as a turn that did not come from a person's request. In a turn that involves people who may talk to it, the Crew also does not read other Crews' settings. With either choice, a change that makes the approval mode less cautious and a change that greatly widens who may talk to it wait for the owner's approval in Cockpit.

When **Interrupting its work** is **Owners only**, messages from other people and other Crews wait and arrive in order after the current work ends.

### Splitting into separate Crews

For example, split them into a "reception Crew" that team members talk to and a "work Crew" that only the owner talks to.

- The reception Crew uses a cautious approval mode and works only in projects it is allowed to touch
- The work Crew has only the owner as a person who may talk to it
- From the reception Crew to the work Crew, only messages are sent. The work Crew treats received messages as data

Because the OS user is the same, this is not complete isolation either. The Crew rules do not stop the reception Crew from reading another Crew's home.

## Terms of AI services

All of a Crew's work, including requests from other people, runs on the owner's AI account (subscription or API key).

Some AI services forbid letting others use an account, or using it through automated means. Check each service's terms, and the owner decides whether to add other people and which account to run on.

Crews cannot run on Antigravity. Google's terms treat use from software other than Google's as a violation that can lead to account suspension.

## What to tell the people in the chat

A Crew sends messages to AI services. Tell the people in the places where the Crew is:

- That a Crew is there, and who runs it
- Which messages are sent to which AI service
- Whom to tell if they want it removed

An example message to pin is in [People and places](https://agi-labo.com/en/tools/cockpit/docs/crew-people). Discord's Developer Policy also requires not using message content to train AI models and not posting automatically just to make a server look active. In Slack too, tell the same things to the people in channels where the Crew listens, and follow Slack's API terms.

## How Chatwork data travels

Chatwork takes a different path from the other chats.

- Chatwork's notices reach Cockpit through AGI Lab. AGI Lab passes on only which messages arrived. Cockpit reads the text and attachments from Chatwork with the API token it stored
- The API token goes into this computer's secure storage. The webhook token is handed to AGI Lab
- Only the person setting up the connection should handle the receiving address and the webhook token. Do not write them in a chat or in your conversation with the Crew
- When you disconnect or delete the Crew, the receiving settings at AGI Lab are also deleted. Delete the Chatwork webhook on the Chatwork side

For details, see [Security and data](https://agi-labo.com/en/tools/cockpit/docs/security-and-data#chatwork-connections-and-data-flow).

## Related pages

- [Approval modes and permissions](https://agi-labo.com/en/tools/cockpit/docs/crew-approval)
- [People and places](https://agi-labo.com/en/tools/cockpit/docs/crew-people)
- [Multiple Crews](https://agi-labo.com/en/tools/cockpit/docs/crew-multiple)
- [Security and data](https://agi-labo.com/en/tools/cockpit/docs/security-and-data)
