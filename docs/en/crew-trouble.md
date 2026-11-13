<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Troubleshooting

What to check when a Crew does not reply, "typing" stays, it stops for approval, cannot connect, or stops at a usage limit or the memory guard, and what common errors mean.

> Verified with AGI Cockpit 4.132.0 on 2026-11-13. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/crew-trouble)

First, open the Crew's conversation in Cockpit. Most reasons appear there.

## No reply

Check these from the top.

| Where to check | What you see | What to do |
| --- | --- | --- |
| Whether Cockpit and the computer are running | | Start them. Messages sent while the Crew was stopped or Cockpit was closed are not delivered. Ask the person to send them again. Only in Chatwork do messages sent while Cockpit was closed arrive later |
| Whether the Crew is running | **Stopped** | Press **Start** in the Crew's row menu, or **Start** in the settings |
| Whether it is waiting for approval | **Allow command execution?** and so on | Press **Allow** or **Deny**. See [Approval modes and permissions](https://agi-labo.com/en/tools/cockpit/docs/crew-approval) |
| Whether it is waiting for a child task | **“(task name)” is waiting for approval**, **“(task name)” is waiting for an answer to an Ask** | Press **Open** to go there and answer |
| Whether it hit a usage limit | **Usage limit reached** | Go to [Stopped at a usage limit](#stopped-at-a-usage-limit) below |
| Whether the memory guard stopped it | A **Resume** button | Go to [Stopped by the memory guard](#stopped-by-the-memory-guard) below |
| The answer is in the conversation but not in the chat | No **✓ Sent · (time)** | Ask it to "send it to the chat." See [How a Crew works](https://agi-labo.com/en/tools/cockpit/docs/crew-work) |
| Whether sending failed | **✕ Failed · (reason)** in the conversation, or **The last send failed, so the next message is sent at (time).** in the settings | Wait, or go to [Sending failed](#sending-failed) below |

**When nothing arrives in the conversation**, the message has not reached Cockpit. Check the following.

- Whether the person is in **Who can talk**. If not, they are in **Waiting for approval**. Go to [A message got ⏳ (waiting for approval)](#a-message-got-waiting-for-approval) below
- Whether the place is **Off**. If the parent channel is off, the Crew does not respond either
- Whether they mentioned the Crew in a place that needs a mention. **Inside threads, a mention is needed at first, even if the channel is set to Everything**
- Whether the message got 🌙. Go to [A message got 🌙 (outside active hours)](#a-message-got-outside-active-hours) below
- Whether the **Connection** tab shows **Connected**

For details, see [People and places](https://agi-labo.com/en/tools/cockpit/docs/crew-people).

## A message got ⏳ (waiting for approval)

When someone who is not in **Who can talk** sends a DM or mentions the Crew, Cockpit adds ⏳ to that message instead of replying. It does not reach the Crew. The same goes for messages from Crews and apps that are not in **Who can talk**.

The owner chooses in **Waiting for approval** in the settings.

- Press **Allow** to remove the ⏳ and deliver the message with ⏳ to the Crew. **Other messages sent while waiting are not delivered.** If needed, ask the person to send them again
- Press **Decline** to remove the ⏳ and deliver nothing

⏳ is added only once per person. If you write text in **Reply for people waiting for approval** in the settings, that text is sent back once along with the ⏳.

## A message got 🌙 (outside active hours)

When someone other than the owner talks to the Crew outside the **Active hours** in the **Schedule** tab, the Crew does not wake up, skips the message, and adds 🌙.

- Skipped messages are not delivered later. Ask the person to send them again during active hours
- The Crew's conversation shows **Skipped (count) messages outside active hours**
- Messages from the owner, input from Cockpit, Autoruns, and Ask answers arrive at any time

## "Typing" does not go away

While the Crew is working, "typing" keeps showing in the chat. It continues even after the Crew sends a progress update. It disappears after 30 minutes without a reply, or when the turn ends. It pauses while waiting for an approval or an answer.

To stop the work, press **Stop** in the message box of the conversation. Only the current turn stops; the connection and the Queue stay.

When the Crew's settings show **At (time), a chat message could not interrupt the turn in progress, because the turn did not stop.**, also try **Stop**. The message stays in the Queue and arrives when the turn ends.

When **Interrupting its work** in the settings is **Owners only**, messages from other people wait in the Queue and arrive after the current work ends.

## It keeps stopping for approval

If it stays in Supervised, it tends to stop every time someone asks from chat. Review the mode.

- The Crew itself stops: set the approval mode in the message box to **Auto**
- Child tasks stop: review **Default approval mode (Native UI)** in the settings

[Approval modes and permissions](https://agi-labo.com/en/tools/cockpit/docs/crew-approval) explains how to choose.

## Sending failed

When the previous send or turn fails, the Crew holds the next message for a while and retries automatically. The wait starts at 30 seconds, doubles as failures continue, and is at most 10 minutes. **The messages stay in the Queue, so they are not lost.**

To send right away, press **Retry delivery** in the Queue, or press **Start a new session**.

When **Delivery unconfirmed** appears, do not resend right away. This is the state where **Check the conversation. Automatic resend is blocked to prevent duplicate delivery.** applies. Check the conversation, and if the message arrived, press **Mark as handled without resending**.

## Cannot connect

What to do depends on what the **Connection** tab shows.

| What it shows | What to do |
| --- | --- |
| **The token is invalid. Paste a new one with Change under the credentials.** | Paste it again with **Change** under the credentials. In Discord, use a token issued again with Reset Token in the Developer Portal |
| **The token is missing. Paste it again with Change under the credentials.** | The same |
| A message beginning with **Message Content Intent is off.** | Turn it on in the app's settings in the Developer Portal, save, and press **Reconnect** |
| A message beginning with **Discord refused the connection.** | Check the token and the Developer Portal settings, then press **Reconnect** |
| **The connection to (chat) dropped. Reconnecting…**, **An error occurred talking to (chat). Reconnecting…** | It reconnects automatically. Wait |
| A message beginning with **This app is used for Ask relay.** or **This app is used by the Crew “(name)”.** | One app can be used in only one place. Use a token from a different app |
| **Not in any server yet** | Invite it to a server with the invite link. You need permission to manage the server |

### Slack connection

| What it shows | What to do |
| --- | --- |
| **The App-Level token (xapp-) is invalid. Paste new tokens with Change under the credentials.** | Paste it again with **Change** under the credentials |
| A message beginning with **The App-Level token (xapp-) and the OAuth token (xoxb-) are not from the same Slack app, so the Crew stopped listening.** | Paste both tokens from the same Slack app again |
| A message beginning with **Socket Mode is off for this Slack app.** | Turn it on in the Slack app's settings, then press **Reconnect** |
| **This Slack app was removed from the workspace. Set up the connection again.** | Set up the connection again. If **Connect the Slack app again** appears, press it |
| **The Crew has not joined this channel** in **Where it responds** | Open the channel in Slack and invite the Crew with `/invite` |

If connecting to Slack stops partway, one of the following messages appears. In every case, you can try again or continue with **Paste tokens by hand instead**.

| Beginning of the message | Likely cause |
| --- | --- |
| **Cockpit could not download the Slack CLI.** | Check the network |
| **The Slack CLI could not run on this computer.** | It may be a company restriction |
| **The Slack CLI did not give a line to paste.** | Try again |
| **Slack did not accept that code.** | Paste the code again instead of typing it. If the line expired, get a new one |
| **Your workspace needs an admin to approve this app.** | Ask your admin |
| **Slack could not create the app.** | Try again |
| **Slack created the app, but Cockpit could not get its tokens.** | Try again |
| **This Crew's Slack app is no longer installed in the workspace.** | Install it again from the app's settings on Slack, or delete the app and try again |
| **Slack created the app, but its tokens did not reach Cockpit.** | Try again |
| **Cockpit got the tokens but could not connect with them.** | Try again |

### Chatwork connection

Chatwork is an AGI Lab member feature. The following messages may appear while connecting and after connecting.

| What it shows | What to do |
| --- | --- |
| A message beginning with **Chatwork for Crews is an AGI Lab member feature.** | Become an AGI Lab member, or sign in to Cockpit with a member account |
| A message beginning with **Cockpit could not confirm your AGI Lab membership, which Chatwork needs.** | Check the network and try again |
| A message beginning with **Chatwork did not accept this API token.** | Copy it again from API Token in the integration settings of the Crew's account and paste it |
| **This Chatwork account is already used by another Crew.** | Make a separate account just for this Crew |
| A message beginning with **Receiving from Chatwork is an AGI Lab member feature.** | Become an AGI Lab member, then try again |
| A message beginning with **You are signed out of AGI Lab.** | Sign in to AGI Lab in Cockpit, then try again |
| A message beginning with **AGI Lab did not accept this Cockpit for Chatwork receiving.** | Update Cockpit, then try again |
| A message beginning with **Your AGI Lab account has as many receiving addresses as it can.** | Disconnect Chatwork from a Crew you no longer use, then try again |
| **Cockpit could not confirm that the receiving address was made. Press Start over.** | Press **Start over** |
| A message beginning with **AGI Lab did not accept this token.** | Copy the token shown on the webhook's screen in Chatwork as it is, and paste it again |
| A message beginning with **The token was changed from somewhere else in the meantime.** | Copy the current token on the webhook's screen in Chatwork and paste it again |
| A message beginning with **The receiving address is gone from AGI Lab.** | Retry to make a new address. Change the URL of the Chatwork webhook to the new address, and paste the token again too |
| A message beginning with **Cockpit could not reach AGI Lab.** | Wait a moment, then try again |
| A message beginning with **The receiving address and the webhook token can be exchanged only over HTTPS or Tailscale.** | Continue in the desktop app |

The Crew may report a failed action in Chatwork like this.

| What it shows | What to do |
| --- | --- |
| A message beginning with **Chatwork rejected the Crew's API token.** | Connect again with a new token in the Crew's settings |
| A message beginning with **The Crew's Chatwork account cannot do this there.** | Check that the Crew's account is a member of the room and is not read-only |
| **The Crew is not a member of that Chatwork room, so it cannot read it.** | Add the Crew's account to that room |
| A message beginning with **Chatwork allows 300 requests every 5 minutes, and the Crew reached that.** | Wait a few minutes |

Chatwork has no threads or reactions. When the Crew says "Chatwork has no threads." or "Chatwork has no reactions, so", it replies by posting in the room or with a message instead.

### Messages while disconnected

For messages sent while the Discord connection was down for a long time, Cockpit fetches them after reconnecting, as long as Cockpit was running. It goes back up to 24 hours and delivers up to 20 messages at a time. If some could not be delivered, the Crew's conversation shows **Some messages from while the chat was disconnected did not reach the Crew**.

In Chatwork, messages that have waited for a minute or more arrive once after Cockpit runs. The same is true while Cockpit was closed. Messages older than the newest 100 in a room do not arrive. When notices are lost at AGI Lab, Cockpit reads again up to 20 rooms that were active recently (going back up to 24 hours), and the owner gets a DM saying "(count) Chatwork notices did not arrive, so recent messages were read again. Some may not have been recovered."

## Stopped at a usage limit

The Crew stops, and chat messages wait in the Queue. The conversation shows **Usage limit reached**.

- When you wait for the limit to reset, messages in the Queue start to arrive
- A message the owner types in the Cockpit conversation is tried once without waiting. When you have upgraded your plan, for example, it can run right away
- **Continue with (agent)** switches to another agent you are signed in to. **This starts a new session, and the conversation so far is not sent to the new agent.** The Crew checks the state before continuing
- Choosing another account in the account selector of the message box switches while keeping the conversation
- If you set the account to **Auto**, it automatically moves to another signed-in account when it hits a limit. The conversation shows **Switched to another account after a usage limit**

### Choosing how to continue from a DM

If Ask DMs are on, **(Crew) stopped at its usage limit** arrives once in the owner's DM. You can choose how to continue with the buttons in the DM.

- **Continue with another account**: switches to another usable account of the same agent. It does not appear when the account is **Auto**
- An agent's name: switches to that agent
- **Wait for the reset**: does nothing and waits for the limit to reset

Only the owner who received the DM can press the buttons. Once a choice is made first in either the DM or Cockpit, the DM changes to show the result. A Crew without an owner gets no DM; choose in the Cockpit conversation.

To use less of your allowance on periodic checks, see [Periodic checks and Autoruns](https://agi-labo.com/en/tools/cockpit/docs/crew-schedule).

## Stopped by the memory guard

When a Mac runs low on memory, Cockpit's memory guard may stop the Crew.

- The conversation is kept. **It does not resume automatically**
- While it is stopped, chat messages, Autoruns, and notices wait in the Queue
- Periodic checks are skipped. The **Schedule** tab shows **Skipped (stopped by the memory guard)**
- If Ask DMs are on, a DM marked 🧠 reaches the owner. This DM has no resume button

To resume, press **Resume** in the Crew's conversation in Cockpit, or have the owner type a message in the conversation. The typed message arrives first, followed in order by the waiting messages.

## Asks do not arrive by DM

**Ask DMs** in the settings shows the reason.

| What it shows | What to do |
| --- | --- |
| **Owners get them once Discord or Slack is connected.** | Finish connecting |
| **Nothing is sent because the Crew has no owner.** | Choose an owner |
| **Nothing is sent because no owner is chosen.** | Choose in **Who gets Ask DMs** |
| A message including **They may not accept DMs from server members in their Discord settings.** | Check in Discord's settings whether DMs from server members are accepted |
| A message including **Slack may not allow this app to message them.** | Check in Slack whether DMs from the app are accepted |

Even if a DM does not arrive, Asks can be answered in Cockpit. Confirmations that widen what the Crew can do, such as approving a settings change, are never sent to DMs in the first place.

## Periodic checks do not run

**Last** for the periodic check in the **Schedule** tab shows why it was skipped, such as **Skipped (outside the hours)**.

| Reason | Meaning |
| --- | --- |
| **outside the hours** | Outside the periodic check hours |
| **working**, **messages waiting**, **waiting for approval** | Other work comes first |
| **stopped**, **restarting** | The Crew is not running |
| **usage limit**, **stopped by the memory guard**, **waiting to retry delivery** | See the sections above |
| **nothing to check** | The Crew declared that it has nothing to do until the next time |

For details, see [Periodic checks and Autoruns](https://agi-labo.com/en/tools/cockpit/docs/crew-schedule).

## How to ask when something is wrong

If you can talk to the Crew, first ask it.

- "Tell me what you are waiting for right now"
- "Did your earlier reply arrive? Check the history"
- "Check your status"

If the Crew seems off, try **Start a new session** or **Start over** in the Crew's row menu. The conversation record and the home are kept.

## What the errors a Crew reports mean

The following words appear in a Crew's reports.

| Error | Meaning | What to do |
| --- | --- | --- |
| `owner_confirmation_requested` | Not an error. It is waiting for the owner's approval | Answer in Cockpit |
| `owner_request_required` | A change not allowed in that turn. Nothing changed | The owner asks directly |
| `not_allowed_for_crew` | An operation the Crew does not do itself | The owner does it in Cockpit |
| `forbidden_from_crew` | An operation only the owner can do | The owner does it in Cockpit |
| `settings_changed` | The settings changed after the request, and the change now needs the owner's approval. Nothing changed | Asking again requests the owner's approval |
| `history_not_allowed` | A place it cannot read in that turn | The owner asks |
| `approval_mode_not_applied` | The mode could not be changed in the current session | Try again after the turn ends |
| `text_too_long` | One post would exceed 10 messages. Nothing was sent | Use a summary and an attachment |
| `unseen_messages` | Before sending, unread messages were found. Nothing was sent | The Crew reads the new messages, then sends again |
| `missing_permission` | The chat permissions are not enough | Review the channel permissions or the invitation |
| `rate_limited` | The chat's rate limit | Wait a little |
| `not_connected` | Not connected right now | Start the Crew |
| `app_in_use` | That app is used by Ask relay or another Crew | Use a different app |
| `git_unavailable` | Git is not installed. The home uses Git | Install Git |
| `instructions_too_long` | `AGENTS.md` exceeded 4,000 characters | Shorten it |
| `host_unreachable` | Cockpit on the other computer does not respond | Check that the other Cockpit is running and remote access is on |

The full list is in the [Crew CLI reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/crew). You can also have the Crew read it and explain.

## Related pages

- [Approval modes and permissions](https://agi-labo.com/en/tools/cockpit/docs/crew-approval)
- [People and places](https://agi-labo.com/en/tools/cockpit/docs/crew-people)
- [How a Crew works](https://agi-labo.com/en/tools/cockpit/docs/crew-work)
- [CLI entry points](https://agi-labo.com/en/tools/cockpit/docs/crew-cli)
- [Reference and support](https://agi-labo.com/en/tools/cockpit/docs/reference-and-support)
