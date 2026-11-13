<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Asks and Crews

How owners answer Asks from Crews and child tasks in Discord, Slack, or Chatwork (for members) DMs, choose recipients, clean up answered DMs, and what never goes to DMs.

> Verified with AGI Cockpit 4.132.0 on 2026-11-13. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/crew-ask)

When a Crew needs a decision, it asks an owner through an Ask. The same goes for Asks from child tasks the Crew hands work to, from the Crew's Autorun tasks, and from Fleets the Crew runs. For how Ask itself works, see [Ask](https://agi-labo.com/en/tools/cockpit/docs/ask).

Like other Asks, a Crew's Asks appear in the Desktop Ask window, the Ask tab of the right side panel, and the PWA **Inbox**. A card also appears in the Crew's conversation. On top of that, a Crew connected to a chat also sends its Asks to owners' Discord, Slack, or Chatwork DMs. You can answer from the DM even when you are away.

## Answer an Ask in a DM

Ask DMs are on from the start. Each owner receives a DM from the Crew's own Discord or Slack app, or from the Crew's Chatwork account.

In Discord and Slack DMs, you can answer the same ways as in Cockpit. For how to answer in Chatwork, see [Answer in Chatwork](#answer-in-chatwork).

- Choose an option with a button or menu
- Answer in text with **✏️ Free input**
- When there are two or more questions, answer them all and press **📤 Submit**, or send one answer with **📝 Reply to the whole ask**. Questions left blank arrive unanswered
- Long choices appear in full, numbered, in the message body. Read the body, then choose with the numbered controls
- Images and videos in the Ask are also attached to the DM, up to 8 (up to 20 MB each)

When there are too many choices to fit in the DM, or the full text exceeds Discord's or Slack's character limit, answer in Cockpit. The DM controls work the same way as in [Ask](https://agi-labo.com/en/tools/cockpit/docs/ask#relay-asks-to-discord-and-slack).

### Answer in Chatwork

Chatwork has no buttons, so you answer by replying with a number.

- Choices arrive with numbers. Reply to the Ask message with the number you chose (for example, 2)
- For questions where you can choose several, send the numbers separated by commas (for example, 1,3)
- For Asks that accept a text answer, you can answer in text instead of a number
- When there are two or more questions, they arrive one at a time. Reply to each one, and the answers reach Cockpit together after the last one. Reply to the first message to answer the whole Ask at once
- DMs that are not replies to an Ask reach the Crew as ordinary requests
- Up to 4 attachments are included. View anything over 5 MB in Cockpit

When a number does not match, a reply such as "Reply with the number of a choice." comes back. Answer again.

**Only the owner who received the DM can answer it there.** If that person is no longer an owner, they can no longer answer.

Wherever you answer, it is reflected everywhere else. When you answer in Cockpit, the DM changes to answered too. The same goes for other owners' DMs. **Close without answering** closes the Ask. The task keeps waiting.

Ask DMs arrive regardless of the DM option in **Where it responds**. Even when DMs are set to **Off**, owners still receive Ask DMs.

## Choose who gets the DMs

The settings are in **Ask DMs** in the **Settings** tab of the Crew's settings.

- Turn off **Send Asks to the owners by DM** to send no DMs. Asks can still be answered in Cockpit
- When there are two or more owners, choose the recipients from **Every owner** or **Chosen owners only**. With **Chosen owners only**, check the owners who should receive DMs

With **Every owner**, owners added later also receive them.

When the Crew is connected to two or more chats, owners appear separately, such as "name (Discord)" and "name (Chatwork)". **Choose which chat receives the DMs by choosing that chat's owner.** For example, if you choose only the Slack owner, DMs arrive only in Slack.

The chosen recipients are also used for Asks about letting in people waiting for approval and for secret input notices.

The Crew can change the recipients on request only when an owner asked.

## Delete answered DMs

At first, DMs are kept after an Ask is answered. Cockpit rewrites the DM to show the result and removes its buttons.

When you turn on **Delete Ask DMs once answered**, the DM is deleted when the Ask is answered, closed, or withdrawn.

- Deleted messages cannot be restored
- The Ask and its answer remain in Cockpit
- When the chat does not allow the deletion, Cockpit rewrites the remaining DM to show the result

Use this when you want to keep your chat DMs tidy.

## What does not go to DMs

| Item | In DMs | Where to answer |
| --- | --- | --- |
| Approval of settings changes the Crew asked for | Not sent | Cockpit |
| Device connection approvals, Fleet gate approvals | Not sent | Cockpit |
| Approval of commands or file edits under the approval mode | Not sent, because it is not an Ask | Cockpit |
| Secret input (passwords and tokens) | Only a notice that input is waiting | Cockpit's secure input screen |
| HTML explanations attached to an Ask | Only one line saying it can be viewed in Cockpit | Cockpit |

Approving a settings change confirms widening what the Crew can do. It is answered only in Cockpit so that it cannot go through even if a chat account is taken over.

Secret input notices arrive only while HTTPS Remote Access is running. A notice contains only the PWA address. It does not contain the value, label, or summary. When the input request ends, Cockpit tells you that too. To enter it from the PWA, you need [Remote Access](https://agi-labo.com/en/tools/cockpit/docs/remote-access).

Content written only in an HTML explanation cannot be read in a DM. Ask the Crew to also put what you need for the decision in the Ask's text or in the descriptions of its choices.

## Asks about letting in people waiting for approval

When someone who may not talk to the Crew talks to it, Cockpit shows owners an Ask titled "Let someone talk to (Crew)?" It includes the first 100 characters of the message and a link to the original message. This Ask also arrives in owners' DMs.

- **Approve** adds that person to the people who may talk to it. The message marked ⏳ reaches the Crew
- **Deny** removes them from Waiting for approval

Only owners can answer. The Crew itself and its child tasks cannot. For details, see [People and places](https://agi-labo.com/en/tools/cockpit/docs/crew-people).

## Other notices that arrive by DM

The following notices are not Asks, but they arrive for the same owners as Ask DMs, under the same settings. They do not arrive if **Send Asks to the owners by DM** is off. They also do not arrive for a Crew without an owner.

### When it stops at a usage limit

When the Crew stops at the agent's usage limit, you receive one DM saying "(Crew) stopped at its usage limit." The DM's buttons let you choose how to continue.

| Button | What it does |
| --- | --- |
| **Continue with another account** | Switches to another account for the same agent that has room under its limit, and continues |
| **Continue with (agent)** | Switches to another available agent and continues |
| **Wait for the reset** | Waits for the limit to reset. If the reset time is known, it appears on the button |

Which buttons appear depends on the accounts and agents available at that time. Only the owner who received the DM can press them.

Whichever choice is made first, in the DM or in Cockpit, takes effect. The same is true when the limit resets and the Crew continues on its own. After that, the DM changes to show the result and can no longer be pressed. DMs from an earlier stop, and buttons pressed after the limit has reset, have no effect.

### When the memory guard stops it

When the memory guard stops a Crew on a Mac, a DM tells you when it happened and what was using memory. This DM has no buttons. Resume the Crew in Cockpit.

## Fix an answer waiting to be sent

If you answer an Ask while the Crew is working, the answer goes into the Crew conversation's queue and shows **Answered · waiting to send**. Until sending starts, you can fix it from the queue on Desktop and in Remote Access.

- **Edit answer in the Ask** withdraws the answer and reopens the Ask with the previous answer filled in
- **Delete answer** withdraws the answer and returns the Ask to empty

In both cases, the withdrawn answer does not reach the Crew. The reopened Ask arrives again as a new message in owners' DMs. The earlier DM stays, still showing the withdrawn answer, and its buttons no longer work.

An answer that is being sent or has been sent cannot be fixed. For how the queue works, also see [Ask](https://agi-labo.com/en/tools/cockpit/docs/ask#what-happens-before-and-after-an-answer).

## When DMs do not arrive

**Ask DMs** in the **Settings** tab shows why they do not arrive. Asks that did not arrive can be answered in Cockpit.

| Message | What to do |
| --- | --- |
| "Owners get them once Discord or Slack is connected." | Connect a chat by following [Connect a chat](https://agi-labo.com/en/tools/cockpit/docs/crew-chat) |
| "Nothing is sent because the Crew has no owner." | Choose an owner in **Who can talk** in the **Connection** tab |
| "Nothing is sent because no owner is chosen." | Check an owner to send to, or go back to **Every owner** |
| "Could not DM (name) (…). They may not accept DMs from server members in their Discord settings. …" | In Discord's settings, allow DMs from members of the servers the Crew is in |
| "Could not DM (name) (…). Slack may not allow this app to message them. …" | Check that the Slack workspace allows DMs from apps |
| "(chat) held back the DM to (name) (…). …" | Cockpit sends it again with the next Ask |

When a DM shows "⏹️ This DM no longer takes answers. Answer in Cockpit.", Ask DMs were turned off, or that owner was removed from the recipients. Answer in Cockpit.

When you press a button in a DM and see "Only this Crew's owners can answer this Ask by DM.", the person who pressed it is not an owner.

## Common pitfalls

- **Only owners can answer by DM.** People only added to the people who may talk to it do not receive Ask DMs
- **Approvals of settings changes do not come by DM.** When the Crew says it is waiting for an owner's approval, open Cockpit
- **Messages deleted with Delete Ask DMs once answered cannot be restored.** The record of answers can be viewed in Cockpit
- **Ask notifications and Ask DMs are different things.** Ask notifications send Asks to the channel chosen in **Ask notifications** in the settings. Ask DMs are sent from the Crew's app only to the Crew's owners

## Related pages

- [Ask](https://agi-labo.com/en/tools/cockpit/docs/ask)
- [People and places](https://agi-labo.com/en/tools/cockpit/docs/crew-people)
- [Connect a chat](https://agi-labo.com/en/tools/cockpit/docs/crew-chat)
- [Troubleshooting](https://agi-labo.com/en/tools/cockpit/docs/crew-trouble)
