<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# How a Crew works

Learn how a Crew goes from a request to a reply, how it handles messages that arrive while it works, how it hands work to child tasks, and how to stop it or start over.

> Verified with AGI Cockpit 4.132.0 on 2026-11-13. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/crew-work)

A Crew treats a request as one piece of work: it takes the request, works on it itself or hands it to a child task, and returns the result to the person who asked.

On this page, a "turn" is one stretch of work in which the Crew acts in response to one request or notice.

## From request to reply

A request from chat goes like this.

1. When a message reaches the Crew, it gets a 👀 reaction. This means "received, not answered yet." It is not added in Chatwork, which has no reactions.
2. When the Crew is mentioned in a channel, it replies in a thread. In Discord, Cockpit creates a thread from that message. In Chatwork, which has no threads, it replies to that message.
3. While it works, "typing" appears where the reply will go.
4. For a request that looks like it will take more than a few minutes, the Crew first replies with one sentence saying what it will do, then goes ahead.
5. The Crew sends its reply. The 👀 is removed.
6. **✓ Sent** appears in the Crew's conversation in Cockpit.

Before asking a person to do some work, the Crew checks whether it can do it itself.

## Replies reach the chat only when sent

**Text the Crew writes in its conversation does not reach the people in the chat.** Even if you can see text in the conversation in Cockpit, it has not necessarily reached the chat.

To reply in the chat, the Crew performs a send action. The conversation shows **✓ Sent** when the reply arrived. If it shows **✕ Failed**, the reason appears with it.

If the answer is written in the conversation in Cockpit but never shows up in the chat, ask the Crew to "send it to the chat."

Long replies are split and sent automatically, up to 10 messages at a time. Beyond that, the Crew writes a short summary and attaches the full text as a file.

### Rereading new messages before sending

If someone else or another Crew has written in a channel or thread since the Crew last looked, Cockpit does not send the Crew's reply there. The Crew reads the new messages first, then decides whether to send the reply as is, revise it, or drop it. This prevents replies that no longer fit the conversation. The reply may arrive a little later. In Chatwork, it checks only the newest 100 messages in the room. Also, Chatwork records send times only to the second, so a message written in the same second the Crew last looked may not stop the reply.

## While "typing" appears

"Typing" appears only in places where the Crew has not replied yet. In Discord it is the typing indicator; in Slack it is the work status inside the thread (thinking, looking things up, and so on). Chatwork has neither a typing indicator nor reactions.

It disappears when:

- The Crew replies in that place. The one-sentence "what I'll do" reply does not make it disappear
- The Crew reacts to a message in that place
- The turn ends
- 30 minutes have passed since the message
- You stop the Crew, start it over, or its connection to the chat drops

It pauses while the Crew waits for an approval or an answer to an Ask.

Once the Crew has replied in a place, "typing" does not appear there again, even when the Crew acts later on an Ask answer or a child task notice. It appears again when a new message arrives in that place.

Even if "typing" cannot be shown, for example because of permissions, the reply still arrives.

## When another message arrives while it works

**Chat messages interrupt the current work.** Cockpit stops the current turn and delivers the message right away. After answering the message, the Crew continues from where it stopped. Even if it looks stopped, the request has not been canceled.

### Choosing who can interrupt

In the Crew's settings, **Interrupting its work** lets you choose who can interrupt.

| Choice | Behavior |
| --- | --- |
| **Everyone** | Messages from anyone who may talk to it interrupt. This is the default |
| **Owners only** | Only the owner's messages and button presses interrupt. Messages from other people and Crews wait in the Queue and arrive in order after the current work ends |

With **Owners only**, waiting messages show **Only owners can interrupt**. You can also change this setting by asking the Crew in chat. It changes only when the owner personally asks.

### What waits instead of interrupting

The following messages do not interrupt; they wait in the Queue until the turn ends.

- Messages sent from the Cockpit message box
- Autorun instructions
- Notices from child tasks

Chat messages also wait in the Queue:

- While the Crew is starting over or stopped
- While the Crew is compacting its conversation
- While the Crew is in the middle of sending a reply
- When Cockpit could not stop the turn. The Crew's settings show **At (time), a chat message could not interrupt the turn in progress, because the turn did not stop.**

If a message from the owner arrives while the Crew is waiting for an approval or an Ask answer, Cockpit sets the waiting approval to **Deny** and cancels the question, then delivers the message. After answering the message, the Crew asks again if it still needs to.

### Sending a queued message now

Messages you send from the Cockpit message box while the Crew is working wait in the Queue of the Crew's conversation. Each queued message has these buttons, in both Desktop and the PWA.

- **Edit queued message**: lets you revise the text before it arrives
- **Send now**: stops the current turn and delivers the message right away. It appears only on the first message in the Queue
- **Remove queued message**: deletes it without delivering it

If you press **Send now** while an approval or question is waiting, Cockpit asks whether it may cancel the waiting approval or question.

When a chat message from someone or a child task notice is waiting in the Queue, the owner can also edit or remove it. If you edit and save it, it arrives as your message. The sender's name stays. Removing it does not tell the sender.

## How to make a request

A Crew answers most easily when the request says:

- What you want
- By when. Include a deadline if there is one
- Where to reply. You can leave this out if the place you asked is fine

The Crew can also receive attachments, up to 25 MB each. Received attachments are deleted after 7 days. For anything you want to keep, ask it to "keep this in the work record."

If you give it a link to a Discord, Slack, or Chatwork message, the Crew can read that message and the ones around it, as far as it has access.

To change or cancel a request, say so in a new message. Editing or deleting the original message after it arrived does not reach the Crew.

## How replies are written

In the Crew's settings, **Chat writing** lets you choose how replies are written.

| Choice | Style |
| --- | --- |
| **Readable** | The conclusion first, organized with bullets, bold, and blank lines. This is the default |
| **Concise** | Only the conclusion, briefly |

A changed style is used from the Crew's next session. It can be changed by asking in chat only when the owner personally asks.

### Replying with tables, charts, and cards

When comparing options or listing numbers, the Crew can post tables, charts, and cards to Discord, Slack, and Chatwork. You can ask "compare them in a table" or "make it a chart." In Discord and Slack they appear as images or blocks, in Chatwork as images and framed text, and in the Cockpit conversation they appear as, for example, **1 table**. When text is enough, the Crew replies in text.

### When it corrects its own posts

When the Crew corrects or adds to an earlier post, it sends the change as a reply to the original post. For a typo only, it edits that post.

## Handing work to child tasks

The Crew hands time-consuming work to child tasks. The Crew decides how to hand it off, but you can also ask:

- "Hand this to a child task"
- "Do this in a Codex child task"

Handed-off tasks appear in the Crew's conversation and in the task list. **Open** takes you to that task.

**Child tasks do not see the Crew's conversation.** The Crew writes the information they need into the request.

**Only the Crew writes in the chat.** The Crew receives a child task's posts or drafts to send, then replies itself.

The Crew follows a child task until it receives the result. Being told "I'll check later" is not enough for it to treat the work as done. A notice reaches the Crew's conversation when a child task completes, asks a question, waits for approval, stops with an error, or stops at a usage limit or because of the memory guard.

For the approval mode of child tasks, see [Approval modes and permissions](https://agi-labo.com/en/tools/cockpit/docs/crew-approval).

## Actions that go outside

The Crew does not ask for confirmation for replies inside the conversation it was asked in. Actions that go outside that conversation are handled by type.

| Action | Examples | Handling |
| --- | --- | --- |
| Inside the conversation it was asked in | Replies, attachments, correcting or deleting its own posts | Done without asking |
| Elsewhere in the same chat | Asked in a DM to post in another channel | Done as is if the request names the destination and content. If it is unclear, the Crew asks the person who made the request |
| Services outside the chat | Email, social media, GitHub, invitations, permission changes, production deployment | Always confirmed with the owner through an Ask |
| Publishing | Publishing to the web, share links | Always confirmed with the owner through an Ask |
| Money, deleting data | Payments, deleting files | Confirmed with the owner through an Ask |

The Crew acts as if it cannot tell the owner apart from other people who may talk to it in chat. So for actions outside the chat, it confirms through an Ask even when the request looks like it came from the owner. Asks are not visible from the chat, so the Crew also replies in that conversation that it is waiting for confirmation.

These are instructions to the Crew. The agent may not always follow them. For details, see [Asks and Crews](https://agi-labo.com/en/tools/cockpit/docs/crew-ask) and [Safety and treating people differently](https://agi-labo.com/en/tools/cockpit/docs/crew-safety).

## Stopping and starting over

There are four ways to stop. They differ in what they keep.

| Action | Where | What stops | What stays |
| --- | --- | --- | --- |
| **Stop** in the message box | The Crew's conversation | Only the current turn | The connection, the session, and the Queue. The next message arrives as usual |
| **Stop** | The Crew's settings | The connection to the chat and the session | The conversation record and context, and the Queue. **Start** picks up where it left off |
| **Start a new session** (**Start over** in the row menu) | The Crew's settings | Ends the current session and starts a new one | The conversation record, the home, and the Queue |
| **Delete Crew** | The Crew's settings | Everything | The home moves to the Trash. The Discord or Slack app and the Crew's Chatwork account stay. Delete the Chatwork webhook in Chatwork |

- **Use Stop in the message box for a turn that does not end.** Starting over waits for the current turn to end before it begins
- **Messages sent while the Crew is stopped are not delivered later.** Cockpit does not go back for messages from the time it was stopped
- **Starting over clears the context.** The Crew continues by reading the records in its home. Have the Crew write down anything you want kept first. You can ask, "Record the current work before you start over"

When you ask the Crew to "start over," it tidies up finished work and brings its records and Mission status up to date before starting over.

## When it hits a usage limit

When the Crew's agent reaches a usage limit, the Crew stops and a notice appears in the conversation in Cockpit. If **Send Asks to the owners by DM** is on, the notice also reaches the owner's Discord, Slack, or Chatwork DM, where the owner can choose how to continue, such as **Continue with another account** or **Wait for the reset**. For details, see [Troubleshooting](https://agi-labo.com/en/tools/cockpit/docs/crew-trouble).

## Related pages

- [People and places](https://agi-labo.com/en/tools/cockpit/docs/crew-people)
- [Asks and Crews](https://agi-labo.com/en/tools/cockpit/docs/crew-ask)
- [Home and memory](https://agi-labo.com/en/tools/cockpit/docs/crew-home)
- [Troubleshooting](https://agi-labo.com/en/tools/cockpit/docs/crew-trouble)
