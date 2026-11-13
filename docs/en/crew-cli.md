<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# CLI entry points

A map of the cockpit crew commands used behind the scenes when you ask a Crew, by use: creating, settings, connecting, people and places, chat actions, Missions, other computers.

> Verified with AGI Cockpit 4.132.0 on 2026-11-13. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/crew-cli)

You do not need to type commands to use Crews. When you use the screens or ask a Crew or the master agent, the agent uses `cockpit crew` commands behind the scenes.

This page is a map of those commands. Use it when you want to know what a command that appears in a Crew's report or work record does. Options and output formats can be read from [Learn more](#learn-more) at the end.

For `<crew>`, enter the Crew's ID or name.

## Creating and viewing

| Command | What it does | Example request |
| --- | --- | --- |
| `cockpit crew list` | Lists Crews | "Show me the list of Crews" |
| `cockpit crew get <crew>` | Shows settings and how it is running. Credentials are not returned | "Tell me the accounting Crew's settings" |
| `cockpit crew status [<crew>]` | Gathers what it is waiting for, child tasks, Asks, and the Queue on one screen | "Tell me what you are waiting for right now" |
| `cockpit crew create --name <name>` | Creates a new Crew | "Create an accounting Crew" |
| `cockpit crew create --from-task <task-id>` | Turns the current task into a Crew, keeping its conversation | Same as **Turn into a Crew** in the task menu |
| `cockpit crew remove <crew>` | Deletes a Crew. The home moves to the Trash | Owner only |

## Settings and running

| Command | What it does |
| --- | --- |
| `cockpit crew update <crew> ...` | Changes the name, agent, model, approval mode, account, periodic check, active hours, place it works in, icon, profile description, chat writing, interrupting its work, and so on |
| `cockpit crew instructions <crew> --get` | Reads `AGENTS.md` |
| `cockpit crew instructions <crew> --set-stdin` | Rewrites `AGENTS.md` and commits it (up to 4,000 characters) |
| `cockpit crew start <crew>` and `stop <crew>` | Starts and stops it |
| `cockpit crew stop <crew> --turn` | Stops only the current turn |
| `cockpit crew restart [<crew>]` | Starts over with a new session |
| `cockpit crew check [<crew>]` | Reads everything a periodic check looks at, all at once |
| `cockpit crew open-home <crew>` | Opens the home folder |
| `cockpit crew send <crew> --text "..."` | Sends a message to a Crew inside Cockpit |

To ask a Crew to do work at set times, use `cockpit autorun`. See [Autorun](https://agi-labo.com/en/tools/cockpit/docs/autorun).

## Connecting a chat

| Command | What it does |
| --- | --- |
| `cockpit crew setup begin <crew> --platform discord` | Starts the connection steps. For Slack, use `--platform slack`; for Chatwork, `--platform chatwork` |
| `cockpit crew connect <crew> --platform discord --secure-input` | Shows a token input field in the owner's Cockpit. The token does not reach the agent. For a Chatwork API token, use `--platform chatwork` |
| `cockpit crew setup slack <crew>` | Goes through the steps to create a Slack app |
| `cockpit crew invite-url <crew>` | Gives the invite link for a Discord server |
| `cockpit crew setup chatwork <crew> --start` | Prepares Chatwork receiving at AGI Lab (AGI Lab members) |
| `cockpit crew setup chatwork <crew> --webhook-url` | Shows the receiving address to paste into the Chatwork webhook |
| `cockpit crew setup chatwork <crew> --webhook-key-stdin` | Hands over the webhook token |
| `cockpit crew setup chatwork <crew>` | Shows how far the Chatwork setup has progressed |
| `cockpit crew setup owner <crew> --yes <user-id>` | Makes the person who sent a DM (in Chatwork, a contact request) the owner |
| `cockpit crew disconnect <crew>` | Removes the connection and deletes the credentials |

Only the owner's side can use `setup chatwork`. The Crew itself and its child tasks cannot.

## People and places

| Command | What it does |
| --- | --- |
| `cockpit crew people add` and `remove` | Adds or removes people who may talk to it |
| `cockpit crew people owner` | Makes someone an owner or removes them as an owner |
| `cockpit crew pending list`, `approve`, and `deny` | Shows, allows, or declines people waiting for approval |
| `cockpit crew place set` and `remove` | Sets how it responds in a channel, or removes the channel from the list |
| `cockpit crew place dm`, `other`, and `threads` | Sets how it responds in DMs, channels not on the list, and threads |
| `cockpit crew place people add` and `remove` | Sets people who may talk to it only in that channel |
| `cockpit crew channels` and `groups` | Lists the channels the Crew can see, and roles or user groups |

## Chat actions

The Crew itself uses these when it replies in chat.

| Command | What it does |
| --- | --- |
| `cockpit crew reply` | Replies to a message |
| `cockpit crew post` | Posts to a chosen place. Tables, charts, cards, and buttons can be included |
| `cockpit crew edit` and `delete` | Corrects or deletes the Crew's posts |
| `cockpit crew react` | Adds or removes a reaction. Not available in Chatwork |
| `cockpit crew thread` | Creates or renames a thread. Not available in Chatwork |
| `cockpit crew history` | Reads recent messages in a place, or a linked message |
| `cockpit crew threads` | Lists the threads it can read. Not available in Chatwork |

## Missions

| Command | What it does |
| --- | --- |
| `cockpit crew mission list` and `get` | Reads the list of Missions and their contents |
| `cockpit crew mission create` | Creates a Mission |
| `cockpit crew mission update` | Writes where it stands, the next step, and what it is waiting for. Pauses or resumes it |
| `cockpit crew mission log` | Adds a record |
| `cockpit crew mission end` | Ends it as achieved or cancelled |

## Other computers

With `--host <host>`, you can work with Crews on another computer owned by the same Tailscale owner. Only `list`, `get`, `status`, `invite-url`, `channels`, `groups`, and `send` can be used. Remote access must be on in the other Cockpit.

| Command | What it does |
| --- | --- |
| `cockpit crew list --host <host>` | Lists the Crews on the other computer |
| `cockpit crew status <crew> --host <host>` | Shows how the other Crew is doing |
| `cockpit crew send <crew> --host <host> --text "..."` | Sends a message to the other Crew. Attachments cannot be sent |

## Who can use them

Even with the same command, what can be done depends on who uses it.

- **The owner's side** (the owner's tasks, the master agent, and shells outside Cockpit) can use all of them
- What **the Crew itself** can do depends on who started the current turn. When refused, `owner_request_required` or `not_allowed_for_crew` is returned
- **The Crew's child tasks** mainly read their own Crew and send messages. They cannot change settings

For details, see [Safety and treating people differently](https://agi-labo.com/en/tools/cockpit/docs/crew-safety) and [Multiple Crews](https://agi-labo.com/en/tools/cockpit/docs/crew-multiple). The meanings of errors are in [Troubleshooting](https://agi-labo.com/en/tools/cockpit/docs/crew-trouble).

## Learn more

- `cockpit help crew`: the authoritative source for Crew commands. How work proceeds is in `cockpit help crew-work`, how to change settings is in `cockpit help crew-settings`, and Missions are in `cockpit help crew-missions`
- [Crew CLI reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/crew): the same content as `cockpit help crew`, readable on the web
- [Cockpit CLI](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli): the overall approach of the CLI, and an explanation of [Crews on other computers](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli#read-and-message-crews-on-another-computer)

## Related pages

- [What is a Crew](https://agi-labo.com/en/tools/cockpit/docs/crew-about)
- [Safety and treating people differently](https://agi-labo.com/en/tools/cockpit/docs/crew-safety)
- [Troubleshooting](https://agi-labo.com/en/tools/cockpit/docs/crew-trouble)
- [Cockpit CLI](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli)
