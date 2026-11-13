<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Multiple Crews

Learn the limit on how many Crews you can have, how to split them, how Crews talk to each other, Crews a Crew creates, mentions from tasks, and Crews on other computers.

> Verified with AGI Cockpit 4.132.0 on 2026-11-13. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/crew-multiple)

You can split Crews by role. Crews can talk to each other both inside Cockpit and in chat.

## How many Crews you can create

AGI Cockpit's local features are free (up to 1 Crew). With an AGI Lab membership, there is no limit.

- **Stopped Crews count too**
- **Crews created by a Crew count too**
- When you are not signed in, the limit is also 1
- When you reach the limit, a notice beginning with "Without an AGI Lab membership, you can have up to 1 Crew, including stopped ones." appears when you try to create one
- Only creating is blocked. **Crews you already have keep working, however many there are.** Starting and stopping them work as usual
- If you delete one of your current Crews, you can create one again

When Cockpit cannot confirm your membership (for example, when offline), you are treated as a member if your membership was confirmed within the last 24 hours. Otherwise, the limit applies until it can be confirmed.

## What is separate and what is shared

Each Crew has its own:

- People who may talk to it and places where it responds
- Place it works in, agent, model, and approval mode
- Session, `AGENTS.md`, memory, and work records
- Chat connections

Some things are not separate.

- **The OS user.** Every Crew runs as the same user. The Crew rules cannot stop one Crew from reading another Crew's home
- **The owner's responsibility.** You can choose a different AI account for each Crew, but the owner is the one who checks the terms

If you connect chats, **each Crew needs its own app.** One Discord or Slack app can be used by only one Crew. An app used for Ask relay cannot be used either. Chatwork also needs a dedicated account for each Crew.

## Talking inside Cockpit

Crews can send messages to each other inside Cockpit. No setup on screen is needed.

- "Ask the accounting Crew for this month's closing date"
- "Ask the research Crew to take this on"

The other Crew receives it as a message from another task.

**The receiving Crew does not treat the message as instructions.** It reads it as data. However, Cockpit marks messages sent in a turn that started from the owner's request. A marked message is handled as the owner's request. The mark does not show that the content is exactly what the owner said.

There are rules for what a Crew can do with other Crews.

| What you want to do | Possible? |
| --- | --- |
| Send a message to another Crew | Yes |
| Read another Crew's settings or `AGENTS.md` | Yes. However, a Crew whose **Changes asked for in chat** is **Only the owner** cannot read them in a turn that involves people who may talk to it |
| Change another Crew's settings | No. The only exception is Crews under it ([below](#having-a-crew-create-crews)) |
| A child task reads another Crew | Only its name and status. It cannot read the settings |

You can also have them discuss things in [Talk Rooms](https://agi-labo.com/en/tools/cockpit/docs/talk-rooms).

## Calling a Crew from a task

Type `@` in the message box of a task's chat, and a **Crews** list appears. The Crew you choose is inserted as a chip with its name and icon.

- The message you send goes to that task's agent. The agent sends a message to the Crew on the chip and asks it
- Clicking a chip in the conversation opens that Crew's conversation
- A chip for a Crew that cannot be found appears faded
- In the PWA on a smartphone, the list opens from the bottom

Crews on other computers also appear in the list. On the desktop, they appear only while other computers are shown in the task list.

## Talking in chat

You can also put Crews in the same channel and have them talk in chat. Add the other Crew to **Who can talk**, with the kind **app**.

When a Crew or app that is not in **Who can talk** speaks to a Crew, it goes into **Waiting for approval**, just like a person. Once you allow it, its messages are delivered.

**The limit on replying back and forth is off at first.** If two Crews keep replying to each other, they keep using up your usage allowance. If you have them talk in chat, turn on **Limit replies between Crews** under **Replies between Crews** in the settings.

- The limit starts at 10. You can choose from 1 to 100
- In a channel, thread, or DM, when messages from other Crews continue this many times with no message from a person, delivery stops there
- When it stops, the Crew is notified
- When a person posts, delivery starts again
- Each message delivered from the other Crew counts as one

When you add an app, also keep in mind that anyone who can operate that app can make requests to the Crew.

## Having a Crew create Crews

If the owner asks, a Crew can create a new Crew. Ask in the Cockpit conversation or in a message from the owner in chat. A request from people who may talk to it cannot create one.

- "Create a Crew to take on the accounting work"

The created Crew is **under** the Crew that created it. The parent's name appears in **Parent Crew** in the settings of the Crew under it. Only the owner can change or remove the parent.

### Letting a Crew manage the Crews under it

In a turn that started from the owner's request, the parent Crew can change the following settings of the Crews under it.

- Name, agent, model, effort, and speed
- Periodic check, place it works in, icon, and profile description
- `AGENTS.md`
- Starting and stopping
- Setting the approval mode to the same as now or more cautious

Examples of requests:

- "Change the accounting Crew's model"
- "Add the month-end steps to the accounting Crew's AGENTS.md"

**A change that makes the approval mode less cautious waits for the owner's approval in Cockpit.** The approval prompt shows the names of both Crews.

Changes appear in the conversation of the Crew under it, showing which Crew changed what. `AGENTS.md` is committed in the home of the Crew under it.

For Crews under another Crew too, only the owner changes the following.

- People who may talk to it and places, chat connections, accounts, and Ask DMs
- **Changes asked for in chat**, **Replies between Crews**, and the parent Crew
- A new session, and deletion

This reaches only the Crews directly under it. It cannot change Crews under those. It also cannot make changes in a turn that started from another Crew's message, so requests are not passed down level after level.

## Crews on other computers

You can also send messages to and from Crews on another computer owned by the same Tailscale owner.

The conditions are:

- Both computers are signed in to the same Tailscale account
- [Remote access](https://agi-labo.com/en/tools/cockpit/docs/remote-access) is on in the other Cockpit. Remote access is for AGI Lab members
- To get replies, remote access is also on in your own Cockpit

There are limits on what you can do.

- Only text can be sent. Attachments cannot be sent
- A message from another computer never counts as a request from the owner personally
- You can read the other Crew's status and settings, but you cannot change them. To change them, use that computer's Cockpit or the [PWA](https://agi-labo.com/en/tools/cockpit/docs/remote-access#operate-a-crew-from-the-pwa)

You ask the same way. Say, "Tell the Crew on the studio computer about this."

## Related pages

- [People and places](https://agi-labo.com/en/tools/cockpit/docs/crew-people)
- [Safety and treating people differently](https://agi-labo.com/en/tools/cockpit/docs/crew-safety)
- [CLI entry points](https://agi-labo.com/en/tools/cockpit/docs/crew-cli)
- [Talk Rooms](https://agi-labo.com/en/tools/cockpit/docs/talk-rooms)
