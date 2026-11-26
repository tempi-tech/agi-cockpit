<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# People and places

Learn how to choose who may talk to a Crew, where it responds, and its active hours, how owners differ from others, and how to let in people waiting for approval.

> Verified with AGI Cockpit 4.140.0 on 2026-11-22. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/crew-people)

Only messages from people who may talk to the Crew, in places where it responds, reach the Crew. You set both in the **Connection** tab of the Crew's settings, separately for Discord, Slack, and Chatwork (Chatwork is for members). For the steps to connect a chat, see [Connect a chat](https://agi-labo.com/en/tools/cockpit/docs/crew-chat). Inside Cockpit, owners can always talk to the Crew.

Open the settings with **Open settings** in the menu on the Crew's row.

## Owners and people who may talk to it

| | Owner | Other people who may talk to it |
| --- | --- | --- |
| How requests are treated | As the strongest instructions | With caution |
| What they can ask for | Everything. The Crew still confirms things such as actions that cannot be undone | Looking things up and answering, and making things inside the home folder. For anything else, the Crew checks with an owner through an Ask |
| Receiving Asks | Can receive and answer them by DM | Do not receive them |

The scope of the people who may talk to it is an instruction to the Crew. **The agent may not follow it, so it is not a guarantee.** For details, see [Safety and treating people differently](https://agi-labo.com/en/tools/cockpit/docs/crew-safety).

A Crew can have several owners, or none.

- The person who pressed **Yes, it's me** in the last step of the connection becomes the owner
- In **Who can talk**, you can make other people owners too. Owners can ask the Crew from chat for owner-only changes. Verified owner requests also widen access without a settings-confirmation Ask
- Without an owner, no one can ask from chat for changes only owners can ask for. They can still be made from Cockpit

**If someone takes over an owner's Discord, Slack, or Chatwork account, the Crew sees them as the owner.** Keep owners' accounts secure.

## Add people who may talk to it

Under **Who can talk**, use **Add**, choose a kind, then pick from **Choose a recent speaker** or the list, or enter an ID.

| Kind | Who is added |
| --- | --- |
| **User** | Only that person |
| **Role** (Discord) | Everyone with that role, including people who get it later |
| **User Group** (Slack) | Everyone in that user group |
| **App** | That app or webhook. Anyone who can run it can give the Crew requests |

You can also add everyone in a Discord server (@everyone) or everyone in a Slack workspace. Everyone in a Slack workspace does not include guests and external people. **Anyone, including people who join later, can then give it requests.** If the approval mode is Full access, those requests run commands and edit files on this computer without asking. When you add people broadly, Cockpit confirms with "Add anyway?"

Requests from other people also run on the owner's AI account. Some AI services forbid letting others use an account, so check each service's terms before adding people.

## Let in people waiting for approval

When someone who may not talk to the Crew sends it a DM, or mentions it in a place where it listens, the message does not reach the Crew, and the person goes into **Waiting for approval**.

- Cockpit adds a ⏳ reaction to the message. It does not reply with text
- Owners receive an Ask about whether to let them in. They can also answer it in a Discord, Slack, or Chatwork DM

⏳ is added only once per person. It is also not added when someone who was declined once talks to the Crew again.

### What reaches the Crew when you allow someone

Press **Allow** under **Waiting for approval** in the **Connection** tab, or choose **Approve** in the Ask, and the person is added to the people who may talk to it.

- **The message marked ⏳ reaches the Crew.** Before delivering it, Cockpit checks again whether it may be delivered for that person and place
- **Other messages sent while they waited do not reach it.** If needed, ask them to send them again
- **Allow lets them talk in DMs and every place the Crew listens to.** To limit them to one channel, do not allow them; add them to **Who can talk only in this channel** for that channel instead

If the Crew is stopped, or an attachment has been deleted and cannot be fetched again, Cockpit only removes the ⏳ and delivers nothing. Choosing **Decline** or **Deny** removes the ⏳ and delivers nothing.

**Waiting for approval** shows where they wrote, the first 100 characters of the message, and **Open message**. Anyone can change their name, so check the ID before allowing someone.

### Messages that do not go to Waiting for approval

**A message from someone who may not talk to it that does not mention the Crew does not go into Waiting for approval.** The same is true in channels set to **Everything**. The message simply does not reach the Crew.

When DMs are set to **Owners only** or **Off**, DMs from other people do not put them into Waiting for approval.

### Chatwork contact requests

In Chatwork, contact requests sent to the Crew's account also go into **Waiting for approval**. Cockpit keeps checking for requests every 10 minutes after the connection.

- **Allow** approves the request in Chatwork, and the person is added to the people who may talk to it
- **Decline** declines the request in Chatwork
- Cockpit approves requests from people already in the list of people who may talk to it by itself

### Crews, apps, and webhooks not in the list

Crews, apps (bots), and webhooks not in the list of people who may talk to it also go into Waiting for approval: when they send a DM, when they mention the Crew, and when they post in a place set to **Everything**. As with people, they get ⏳ and owners receive an Ask. The Ask says that it is not a person.

When allowed, they are added to the people who may talk to it as an **App**. Anyone who can run it can then give the Crew requests, so Cockpit confirms before adding it. Apps and webhooks cannot be owners.

### A note to people waiting

At first, Cockpit only adds ⏳ and does not reply with text. If you write text in **Reply for people waiting for approval**, Cockpit also sends that text once per person, in addition to ⏳.

| Place | How it is sent |
| --- | --- |
| Discord | As a reply to their message |
| Slack | As a message only that person can see |
| DM | As a normal message |

The text is sent exactly as written; AI does not write it. It is not sent to Crews, apps, or webhooks.

## Where it responds

In **Where it responds**, choose how the Crew responds in each place.

| Place | Options |
| --- | --- |
| Added channels | **When mentioned** / **Everything** / **Off** |
| **Other channels** | **When mentioned** / **Off** |
| **DM** | **Everyone who can talk** / **Owners only** / **Off** |
| **Threads this Crew created** | **When mentioned** / **Everything** |
| **Threads joined later** | **When mentioned** / **Everything** |

Each works as follows.

- **When mentioned:** Messages that mention the Crew reach it
- **Everything:** Every message from people who may talk to it reaches the Crew, even without a mention. This also increases what is sent to the AI service
- **Everyone who can talk for DMs:** The Crew receives DMs from people who may talk to it. Not everyone's DMs reach it
- **Owners only for DMs:** The Crew receives only owners' DMs. Without an owner, this is the same as **Off**
- **Off for DMs:** The Crew does not receive even owners' DMs

A new Crew's DMs start at **Owners only**. The settings of existing Crews do not change. With **Everyone who can talk**, a request from anyone who can talk runs on your AI account and this computer, and the attachments they send are received too, so Cockpit asks you to confirm first, and the CLI needs `--confirm`.

The DM setting does not affect the Ask DMs the Crew sends to owners.

### What counts as a mention

These messages reach the Crew as mentions of it.

- Messages that mention the Crew
- In Discord, replies to the Crew's messages. This is the same even if the reply's @ is turned off
- In Discord, mentions of the Crew's own role. This is the role with the Crew's name that Discord gives the Crew
- In Chatwork, messages that address the Crew with TO, and replies to the Crew's messages

Mentions of other roles, @everyone, and @here are not mentions of the Crew. TO ALL alone in Chatwork is not a mention of the Crew either.

### Add a channel

Choose from the list with **Add a channel**.

- **Discord:** The list shows only channels the Crew can see. A missing channel appears once you give the Crew the **View Channel** permission in Discord
- **Slack:** The list shows public channels and private channels the Crew has joined. For a channel the Crew has not joined, send `/invite @(Slack handle)` in that channel

### A mention in a channel you have not added

On Discord and Slack, when **Other channels** is **Off**, a mention in a channel you have not added waits for an owner's answer. Cockpit marks the first message with ⏳ and sends an Ask showing the channel, sender, first 100 characters, and message link. In a thread, the Ask concerns its parent channel.

- **React when mentioned** is the recommended default. **React to every message** also allows future messages from people who may talk to the Crew without a mention.
- If the sender may not talk to the Crew yet, the same Ask offers **In this channel only**, **Everywhere** (DMs and every place the Crew listens to), or **Don't add**. Adding the channel alone does not give everyone permission to talk.
- Cockpit holds the oldest 10 mentions in that channel. After approval, it checks current permissions again and delivers eligible mentions in arrival order.
- **Don't add** for the channel sets it to **Off**, discards the waiting mentions, and stops future approval requests there. Closing the Ask without answering discards those mentions; a new mention can ask again.

Only the owner can answer. These Asks can arrive by DM when Ask DMs are enabled. Explicitly disabled channels do not produce an Ask. This channel approval flow does not apply to Chatwork.

### Set how an individual thread receives messages

From v4.140.0, **Per-thread settings** in the connection's **Where it responds** lets you choose **Follow defaults**, **All messages**, **Mentions only**, or **Off** for one Discord thread or forum post, or one Slack thread. Add a thread by its link or place ID. Chatwork and Google Chat do not support these settings. Each connection can keep up to 500 explicit thread settings; returning one to **Follow defaults** removes that setting.

**Follow defaults** follows later changes to the shared settings. Without an individual setting, Discord forum posts and Slack threads under an **Everything** parent receive all messages from allowed people. Other Discord threads follow the created/joined defaults; threads the Crew has not joined and older threads with an unknown origin require mentions.

A parent channel set to **Off** stops its threads even if a thread is set to **All messages**. If the parent is unlisted and **Other channels** is **Off**, add the parent first: an individual thread setting cannot grant access to it. Every change, including **Off** and **Follow defaults**, must freshly confirm the thread and parent with Discord or Slack. If confirmation fails, nothing is saved. A damaged saved setting receives nothing; an unconfirmed parent is shown as unknown rather than presumed accessible.

The screen shows the effective mode and its reason. **All messages** still accepts only people allowed to talk to the Crew, and existing active-hours and other restrictions remain. Changes affect future arrivals; earlier messages are not delivered again. Slack thread labels use the channel and root-message time, not message text.

When the Crew handles a request from someone other than the owner, widening a thread to receive every message needs owner approval. This includes **Follow defaults** when it would receive everything, and a parent-channel change that re-enables a thread set to **All messages**. The owner's Ask identifies affected threads. If settings change before the answer so that more threads would receive everything, Cockpit refuses the stale approval and asks again. `--confirm` cannot replace owner approval. Narrowing to **Off** applies immediately; changes to mentions follow the existing rules for who may change places.

For CLI operations, use `cockpit crew place set <crew> --thread <link-or-place-id> --mode all|mention|off|inherit`, then inspect `cockpit crew place get <crew> --place <link-or-place-id>`. With multiple connections, specify the platform when the ID does not identify it. See the [Crew reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/crew).

### When messages in a channel do not reach it

When the Crew cannot use a channel, the reason appears on that channel in **Where it responds**.

| Message | What to do |
| --- | --- |
| "The Crew can't see this channel" or a similar missing permission | In Discord, open **Edit Channel → Permissions**, choose the Crew with **Add members or roles**, and turn on whichever of **View Channel**, **Send Messages**, and **Read Message History** is missing |
| "The Crew has not joined this channel" | Send `/invite @(Slack handle)` in that Slack channel |

When you fix it in Discord or Slack, the message updates automatically.

## Active hours

In **Active hours** in the **Schedule** tab, you can choose the days and hours when the Crew responds to chats from people other than owners. It starts as **Any time**.

1. Choose **Set hours**
2. Enter the times for **Active hours start** and **Active hours end**
3. Under **Days**, choose the days it responds

Outside active hours, it works as follows.

- **Mentions, DMs, replies, and button presses from other people do not reach the Crew, and get a 🌙 reaction.** It does not reply with text. They are not delivered later when active hours begin
- The Crew's conversation shows one line, "Skipped (count) messages outside active hours"
- Owners' messages, input from Cockpit, Autoruns, Ask answers, and notices from child tasks always arrive
- Work in progress continues after active hours end

Times are counted with this computer's clock and time zone. Hours that cross midnight belong to the day they start. For example, Monday 22:00 to 6:00 includes Tuesday until 6:00. The periodic check hours are set separately. For details, see [Periodic checks and Autoruns](https://agi-labo.com/en/tools/cockpit/docs/crew-schedule).

## Ask the Crew to change them

You can also ask the Crew to change the people and places.

- "Add @alex to the people who can talk to you"
- "In #general, respond only when mentioned"

However, changes that widen access follow these rules.

| Change | Whose request it accepts |
| --- | --- |
| Removing, turning off, setting DMs to **Owners only** | Anyone's |
| Adding one user, allowing a person waiting for approval, adding a channel as **When mentioned** | Owners and people who may talk to it. If you set **Changes asked for in chat** in the settings to **Only the owner**, only owners |
| Adding roles, user groups, everyone, or apps, setting **Everything**, allowing Crews, apps, or webhooks waiting for approval | Applies directly for an owner request. An allowed person’s request needs the owner’s approval in Cockpit |

For changes that need an owner's approval, an approval Ask appears in Cockpit. This Ask is not sent to Discord, Slack, or Chatwork DMs.

## Tell the people there

The Crew sends the messages it receives to the agent's AI service. Tell the people in the servers and channels where the Crew is. Discord's developer policy requires telling users how their data is used. Tell people the same way in Slack, and follow Slack's API terms. For example, pin a message like this.

> This server has (name), an AI assistant run by (owner). Messages that mention (name), and messages in channels where (name) answers, are sent to (AI service) so it can answer. If you want it removed, or do not want it to read a channel, tell (owner).

## Related pages

- [Connect a chat](https://agi-labo.com/en/tools/cockpit/docs/crew-chat)
- [Asks and Crews](https://agi-labo.com/en/tools/cockpit/docs/crew-ask)
- [Approval modes and permissions](https://agi-labo.com/en/tools/cockpit/docs/crew-approval)
- [Safety and treating people differently](https://agi-labo.com/en/tools/cockpit/docs/crew-safety)
- [Troubleshooting](https://agi-labo.com/en/tools/cockpit/docs/crew-trouble)
