<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Connect a chat

Connect a Crew to Discord, Slack, or Chatwork (Chatwork for members). Covers what a person does, asking the Crew to connect, failures, and how the chats differ.

> Verified with AGI Cockpit 4.132.0 on 2026-11-13. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/crew-chat)

Connect a Crew to Discord, Slack, or Chatwork (Chatwork is for members) to talk to it there. Even without a chat connection, you can talk to the Crew inside Cockpit. One Crew can be connected to all three.

You connect it in the **Connection** tab of the Crew's settings. Choose **Open settings** from the menu on the Crew's row, then pick the **Discord**, **Slack**, or **Chatwork** tab under **Connection**. If it is not connected yet, **Start setup** appears.

While you go through the steps, **Connection setup** on screen shows which step you are on (for example, 2/3). Go back to a finished step with **Redo**. To stop, press **Cancel**. This deletes the settings and credentials entered so far.

## Before you start

| | Discord | Slack | Chatwork |
| --- | --- | --- | --- |
| Number of steps | 3 (app and token, server invitation, owner) | 2 (app, owner) | 3 (account and API token, webhook, owner) |
| What you need | A Discord account and permission to manage the server the Crew will join | A Slack account. Some workspaces need an admin's approval | An AGI Lab member account and a Chatwork account just for the Crew |
| Rough time needed | About 3 minutes | About 1 minute | About 10 minutes (including making the Crew's account) |

**One Discord or Slack app can be used either for one Crew or for Ask notifications, not both.** If you use the same app as Ask notifications, you see "This app is used for Ask relay. Use a token from a different app for the Crew." Create a separate app for each Crew. Chatwork works the same way: one Chatwork account can be used by only one Crew.

## Connect to Discord

In the **Discord** tab, press **Start setup**.

### 1. Create an app and paste its token

A person does this part in Discord's screens.

1. Open Discord's Developer Portal with **Open Developer Portal**
2. Create an app with New Application. This becomes the Crew's Discord account
3. In the app's settings, press Reset Token and copy the token that appears
4. Go back to Cockpit and paste it into the **Discord token** field. Cockpit checks it as soon as you paste it

If it is correct, "Verified as (app name)" appears and you move to the next step.

**You do not have to turn on Message Content Intent yourself.** Cockpit turns it on automatically when it checks the token. However, an app used in 100 or more servers needs Discord's review, so Cockpit cannot turn it on. In that case, turn it on yourself on the same Developer Portal screen, save, and press **Verify** again.

### 2. Invite it to a server

1. Press **Open invite link**. Discord opens in your browser
2. Choose the server the Crew should join and press "Authorize"

Once the Crew joins the server, you move to the next step automatically.

**Only people who can manage a server can add an app to it.** Neither Cockpit nor the Crew can do this for you. If you are not an admin, give the invite link to an admin with **Copy link** and ask them to add it.

### 3. Send the Crew a DM

Last, the person who will be the owner sends a DM to the Crew from their own Discord account. Open the Crew's profile with **Open (name) in Discord**, then send it from "Message". For the rest, see [Choose an owner](#choose-an-owner).

## Connect to Slack

In the **Slack** tab, press **Start setup**. There are two ways to connect Slack. Normally, let Cockpit create the app.

### Connect with one line and a code

Cockpit installs the official Slack CLI in a dedicated folder and creates a Slack app for this Crew. The app's keys go into this computer's secure storage. No one handles a token.

1. If needed, enter a **Slack handle (letters and numbers)**. This is the name used to mention it in Slack, and it may contain only letters and numbers. The Crew's name appears as the app's name
2. Press **Connect to Slack**
3. **Copy** the line shown on screen, paste it anywhere in Slack (your own DM is fine), and send it
4. On the screen Slack shows, press "Allow". A short code appears
5. Paste that code into the **Code from Slack** field in Cockpit and press **Confirm**

Cockpit creates the app and adds it to the workspace. When "Connected to Slack." appears, you move to the next step. The new Crew's app sends a DM to the person who set it up.

**The line expires in about a minute.** Paste it into Slack right away. If it expires, Slack shows an error and no code appears. In that case, press **Slack showed an error? Get a new line**.

Paste the code instead of typing it. Some characters, such as I and l, are hard to tell apart.

After you have signed in once on this computer, the second and later Crews may connect with just **Connect to Slack**.

### Create the app by hand

When the Slack CLI cannot run, for example because of company restrictions, use **Paste tokens by hand instead** to create the app yourself and connect it.

1. Press **Create the app with these settings**. Slack's app creation screen opens with Cockpit's settings filled in. When the settings are long, the button copies them before opening Slack, so choose "From a manifest", choose JSON, and paste them
2. Install the app you created in the workspace
3. From **Open "Install App"**, copy the OAuth token (starting with `xoxb-`) and paste it into the **OAuth token (xoxb-…)** field
4. From **Open "Basic Information"**, create an App-Level Token (starting with `xapp-`) with the `connections:write` scope, copy it, and paste it into the **App-Level token (xapp-…)** field
5. Press **Verify**

The two tokens must come from the same app, or the connection fails.

### Workspaces that need admin approval

Some workspaces need an admin's approval to add an app. If "Your workspace needs an admin to approve this app." appears, ask your admin and then try again, or create the app by hand.

### Invite the Crew to a channel

A Slack Crew joins the workspace when you connect it. There is no invite link like in Discord.

**To talk in a channel, you need to invite the Crew to that channel.** Send `/invite @(Slack handle)` in the channel. Use the name set in **Slack handle (letters and numbers)**, not the display name.

Messages that mentioned the Crew in that channel within the 10 minutes before you invited it reach the Crew once when you invite it. You do not need to write them again after inviting it.

## Connect to Chatwork

**Chatwork is an AGI Lab member feature.** First, sign in to Cockpit with your AGI Lab member account. If you are not a member, a message starting with "Chatwork for Crews is an AGI Lab member feature." appears when you paste the API token, and it does not connect.

**The Crew needs a Chatwork account just for the Crew.** You do not use your own account. The Crew talks in Chatwork as that dedicated account.

In the **Chatwork** tab, press **Start setup**. At the start of the steps, "An AGI Lab member feature. Takes about 10 minutes, including making the Crew's account." appears.

### 1. Make the Crew's Chatwork account and paste its API token

A person does this part in Chatwork's screens.

1. Open the sign-up page with **Open Chatwork sign-up** and register an account just for the Crew with an email address, a name, and a password
2. Do the "I'm not a robot" check, the phone check (a call from a phone line in Japan), and the link in the email yourself. Neither the Crew nor Cockpit can do these for you
3. Still signed in as the Crew's account, open API Token in Chatwork's integration settings with **Open API Token page**, and copy the token
4. Go back to Cockpit and paste it into the **Chatwork API token** field. Cockpit checks it as soon as you paste it

If it is correct, "Verified as (name)" appears and you move to the next step.

Tips for making the account:

- With Gmail, you can sign up with an alias that has a "+", such as `you+crew@gmail.com`. Its mail arrives in your usual inbox
- The free plan works. However, it allows up to 20 contacts outside your organization, and you can read only the last 40 days of messages
- On a company plan, an admin can add the Crew as a member instead

### 2. Create a webhook in Chatwork

Chatwork's notices reach Cockpit through AGI Lab. In this step, you paste that receiving address into a Chatwork webhook.

After "Preparing the receiving address…", the steps appear.

1. Still signed in as the Crew's account, open Chatwork's Webhook page with **Open Webhook page** and start creating a new webhook
2. For the event, choose "Account event"
3. In Cockpit, press **Show address**, **Copy** the address that appears, and paste it into the Webhook URL field in Chatwork
4. When you create it in Chatwork, a token appears. Copy it, paste it into the **Webhook token** field in Cockpit, and press **Save**

After "Handing the token to AGI Lab…" appears, you move to the next step. The finished step shows "Waiting for the first notice…" until the first notice arrives, and "Connected" once it does.

**Only the person creating the webhook should handle the receiving address.** Do not leave it in a chat or a note.

In the PWA, the receiving address and the webhook token can be exchanged only when you are connected over HTTPS or Tailscale. Otherwise, "The receiving address and the webhook token can be exchanged only over HTTPS or Tailscale. …" appears, so continue in the desktop app.

### 3. Add it as a contact yourself

The screen says **Send the Crew a contact request to make yourself its owner**.

Last, the person who will be the owner sends a contact request to the Crew's account from their own Chatwork account. You can find the Crew's account by the email address used to sign it up. Chatwork has no invitation step.

During this step, Cockpit checks for requests that reached the Crew's account every 15 seconds. When a request arrives, "🔔 (name) sent a contact request. Is this you?" appears. For the rest, see [Choose an owner](#choose-an-owner).

### What reaches the Crew in Chatwork

These messages reach the Crew. As in the other chats, whether they reach it depends on **people who may talk to it** and **Where it responds**.

- DMs to the Crew's account
- Messages that address the Crew with TO
- Replies to the Crew's messages
- Files attached to messages (up to 25 MB each)

TO ALL alone does not count as a mention of the Crew.

Messages sent while Cockpit was closed also reach the Crew once, the next time Cockpit runs. However, messages older than the newest 100 in a room do not arrive. Anything that did not arrive appears in a notice to the Crew. When notices are lost at AGI Lab, the owner gets a DM saying "(count) Chatwork notices did not arrive, so recent messages were read again. Some may not have been recovered."

Messages deleted before they arrive, and messages in rooms the Crew has left, do not arrive. Messages edited before they arrive arrive with the edited text. **Edits and deletions after a message has arrived do not reach the Crew.** To change or cancel a request, write a new message.

### How the Crew replies in Chatwork

- The Crew sends its replies as Chatwork replies. The other person gets a notification
- Tables and charts appear as images, up to 4 per post. Cards appear inside a Chatwork frame
- Formatting such as bold is removed. Code appears in a Chatwork code frame
- When it asks you to choose, a numbered list appears at the end of the post. Reply to that post with a number to choose. Once you choose, the list changes to "✅ (your choice)", and you can choose only once per post
- There are no threads, reactions, or typing indicator

You also answer Ask DMs by replying with a number. For details, see [Asks and Crews](https://agi-labo.com/en/tools/cockpit/docs/crew-ask).

## Choose an owner

For both Discord and Slack, in the last step, the person who will be the owner sends a DM to the Crew from their own account. Only that person can do this. In Slack, reply to the DM the Crew sent, or find the Crew by name and send it a message.

When the DM arrives, Cockpit shows "🔔 A DM arrived from (name). Is this you?"

- If it is you, press **Yes, it's me**. That person becomes the owner, and the connection is complete
- If it is someone else, press **Someone else** and wait for the next DM
- To go on without choosing an owner, press **Continue without an owner**

**Before pressing Yes, it's me, check that the ID next to the name is yours.** Anyone can change their name.

Only people who send the Crew a DM during this step can become the owner. Just mentioning it in a channel does not make someone a candidate. You can add or remove owners later. For details, see [People and places](https://agi-labo.com/en/tools/cockpit/docs/crew-people).

**In Chatwork, the owner is chosen by a contact request instead of a DM.** While waiting, "Waiting for a contact request…" appears. The first person to send a request becomes the candidate. When you press **Yes, it's me**, that person becomes the owner and Cockpit approves the request. Requests from other people go to **Waiting for approval**.

## Ask the Crew to connect

You can also ask the Crew to "connect to Discord." However, a person still does the parts only a person can do.

| Step | Who does it |
| --- | --- |
| Create the app and get the token | A person. The Crew walks them through it |
| Hand over the token | A person. The Crew shows a secure input field, and the person pastes the token there |
| The Slack line and code | A person, in Cockpit's settings screen |
| Make the Crew's Chatwork account | A person. Only a person can complete the identity checks when signing up |
| Hand over the Chatwork API token | A person. The Crew shows a secure input field, and the person pastes the token there |
| The Chatwork webhook | The owner, in Cockpit's settings screen. The Crew cannot do it |
| Invite it to a server | A person who can manage the server. The Crew can hand over the invite link |
| Choose an owner | The person who will be the owner sends a DM themselves (in Chatwork, a contact request) and presses **Yes, it's me** in Cockpit |

The secure input field appears in the same places as Asks: on Desktop, in its dedicated window and the Ask tab of the right side panel; in the PWA, in **Inbox**. You can paste in the PWA only when connected over HTTPS. When you paste the token and press **Connect with this token**, Cockpit checks the token and stores it in this computer's secure storage.

**The token never reaches the Crew.** The Crew is told only whether it connected or where it failed. Do not write a token in a chat or in your conversation with the Crew.

The Crew can show the input field only when an owner asked for it.

## After connecting

After you connect, the **Connection** tab shows these rows.

| Row | What you can do |
| --- | --- |
| State | Shows **Connected**, **Reconnecting**, **Error**, **Stopped**, and so on, with an explanation of any error |
| **Servers** (in Slack, **Workspace**; in Chatwork, **Organization**) | Where the Crew is. In Discord, **Copy invite link** lets you invite it to other servers too |
| Credentials | **Change** lets you paste a new token. The people who may talk to it and the places it responds stay as they are |
| **Disconnect** | Deletes the credentials and the settings for people and places. The Discord or Slack app and the Crew's Chatwork account stay |

When you disconnect Chatwork, the receiving address at AGI Lab is also deleted and the webhook stops working. Delete the Chatwork webhook on the Chatwork side too.

Cockpit does not change the name, icon, or profile of the Crew's Chatwork account. Change them in Chatwork.

In Discord, to let the Crew notify @everyone, @here, and roles, give the Crew's role the "Mention @everyone, @here, and All Roles" permission in the server settings, or invite it again with the invite link.

## When it does not connect

Tell the cause from what the **Connection** tab shows.

| Message | What to do |
| --- | --- |
| "The token is invalid. Paste a new one with Change under the credentials." | Issue the token again with Reset Token in Discord, or on the "Install App" page in Slack, and paste it |
| "Paste a token reissued with Reset Token in the Developer Portal." | The token you pasted is old or wrong. Issue it again with Reset Token |
| "Cockpit could not turn on Message Content Intent. …" | Turn it on yourself in the Developer Portal, save, and press **Verify** again |
| "Message Content Intent is off. …" | Likewise, turn it on, save, and press **Reconnect** |
| "Discord refused the connection. …" | Check the token and the Developer Portal settings, then press **Reconnect** |
| "This app is used for Ask relay. …" or "This app is used by the Crew "(name)". …" | Create a different app and use its token |
| "The connection to Discord dropped. Reconnecting…" (in Slack, "The connection to Slack…") | Cockpit reconnects by itself. Wait |
| The state is **Stopped** | The Crew is stopped. Press **Start and reconnect** |
| "Not in any server yet" | Add the Crew to a server with the invite link |
| "Slack did not accept that code. …" | Paste the code again instead of typing it. If the line expired, get a new line |
| "The Slack CLI could not run on this computer. …" or "Cockpit could not download the Slack CLI. …" | Check the network and try again, or create the app by hand |
| "Your workspace needs an admin to approve this app. …" | Ask your admin, or create the app by hand |
| "The App-Level token (xapp-) and the OAuth token (xoxb-) are not from the same Slack app, so the Crew stopped listening. …" | Copy both from the same app and paste them again |
| "Socket Mode is off for this Slack app. …" | Turn on Socket Mode in the Slack app's settings and press **Reconnect** |
| "This Slack app was removed from the workspace. …" | Set up the connection again |
| "The Crew has not joined this channel" | Send `/invite @(Slack handle)` in that channel |
| "Chatwork did not accept this API token. …" | Copy it again from API Token in the integration settings of the Crew's account and paste it |
| "Receiving from Chatwork is an AGI Lab member feature. …" | Become an AGI Lab member, then try again |
| "You are signed out of AGI Lab. …" | Sign in to AGI Lab in Cockpit, then try again |
| "AGI Lab did not accept this token. …" | Copy the token shown on the webhook's screen in Chatwork as it is, and paste it again |
| "The receiving address is gone from AGI Lab. …" | Retry to make a new address. Change the URL of the Chatwork webhook to the new address |

When the token of a Slack app Cockpit created stops working, **Connect the Slack app again** appears. Press it and Cockpit gets a new token. If the app no longer exists in Slack, Cockpit creates a new app. **A new app has a different Slack ID, so you need to invite it to your channels again with `/invite`.** The channels to invite it to again are shown on screen.

For how to fix missing channel permissions, see [People and places](https://agi-labo.com/en/tools/cockpit/docs/crew-people). For anything else, see [Troubleshooting](https://agi-labo.com/en/tools/cockpit/docs/crew-trouble).

## How the chats differ

| | Discord | Slack | Chatwork |
| --- | --- | --- | --- |
| Who can use it | Anyone | Anyone | AGI Lab members |
| The Crew's account | A person creates an app in the Developer Portal and pastes the token | Cockpit creates the app (you can also create it by hand) | A person makes an account just for the Crew and pastes its API token |
| Joining a server or workspace | With the invite link. Needs permission to manage the server | Joins when connected. There is no invite link | No invitation step. You create a webhook instead |
| Talking in a channel or room | The Crew only needs permissions in the channel | Invite it with `/invite` in the channel | The Crew's account only needs to be a member of the room (read-only does not work) |
| Replies to mentions | Replies in a new thread | Replies in the thread of that message (the same in DMs) | Replies with a Chatwork reply. There are no threads |
| Threads in channels set to **Everything** | Need a mention at first | Arrive without a mention | There are no threads |
| Replies to the Crew's messages | Treated the same as a mention | Not treated as a mention | Treated the same as a mention |
| Notifying many people at once from the Crew | @everyone, @here, roles | @channel, @here, user groups | TO ALL |
| While working | Shows the typing indicator | Shows one line in the thread with the kind of work it is doing now | Nothing |
| Name used for mentions | The Crew's name | **Slack handle (letters and numbers)** | Choose the Crew's account as the TO recipient |
| When it asks you to choose | Buttons | Buttons | Reply with a number from the numbered list |

In Discord, mentions of the Crew's own role also reach the Crew as a mention of it. To add many people who may talk to it at once, use roles and everyone in the server (@everyone) in Discord, and user groups and everyone in the workspace (not including guests and external people) in Slack.

Slack also differs in these ways.

- Editing or deleting a message does not reach the Crew. To change or cancel a request, write a new message
- In group DMs, only messages that mention the Crew reach it
- Go through the Slack connection steps on the computer where Cockpit is running. In the PWA, you can only see the progress

How Chatwork differs is summarized in [What reaches the Crew in Chatwork](#what-reaches-the-crew-in-chatwork) and [How the Crew replies in Chatwork](#how-the-crew-replies-in-chatwork).

## Common pitfalls

- **In Slack, the Crew appears online even when Cockpit is not running.** Slack's settings only make it look that way. It can reply only while Cockpit is running
- **Nothing reaches the Crew from a Slack channel until you invite it with `/invite`.** Adding the channel to **Where it responds** is not enough until you invite it
- **Adding the Crew to a Discord server needs permission to manage the server.** If you are not an admin, give the invite link to an admin
- **An app used for Ask notifications cannot be used for a Crew.** The reverse is also true
- Disconnecting leaves the Discord or Slack app in place. If you no longer need it, delete it on the Discord or Slack side
- **Chatwork needs an account just for the Crew.** The Crew talks as the account whose API token you pasted. Do not paste the token of your own account
- **The Chatwork webhook event is "Account event".** Stay signed in as the Crew's account when you create it
- When you disconnect Chatwork, delete the Chatwork webhook too

## Related pages

- [People and places](https://agi-labo.com/en/tools/cockpit/docs/crew-people)
- [Asks and Crews](https://agi-labo.com/en/tools/cockpit/docs/crew-ask)
- [Create your first Crew](https://agi-labo.com/en/tools/cockpit/docs/crew-first)
- [Troubleshooting](https://agi-labo.com/en/tools/cockpit/docs/crew-trouble)
- [Ask](https://agi-labo.com/en/tools/cockpit/docs/ask)
