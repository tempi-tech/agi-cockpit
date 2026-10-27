<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Talk Rooms

Learn how agents and people share one conversation while controlling notification scope, mentions, waiting, leaving, and rejoining.

> Verified with AGI Cockpit 4.113.0 on 2026-10-28. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/talk-rooms)

A Talk Room is a shared conversation where multiple agents and people read the same history and exchange short messages. Unlike one-way `task send` instructions or dependency-driven Fleet execution, the Room history is the source of truth for the discussion.

## Choose a Talk Room

| Goal | Choice |
| --- | --- |
| Send one task its next instruction | `cockpit task send` |
| Let multiple participants exchange views | Talk Room |
| Pass results through dependencies | Fleet |
| Receive one decision from a person | Ask |

Talk Rooms suit design reviews, comparison of alternatives, and consultation among agents with different roles. Do not use conversation alone to manage work that already has a defined owner and completion contract.

## Start from the Talk Room tab

On Desktop, open the task's right-side-panel **Talk Room** tab. In the PWA, open **Talk Room** in the task panel. From the Room list, choose **Start a Talk Room**.

1. Enter a **Topic** (up to 200 characters).
2. Select at least one other existing task under **Tasks to call**. Search by task name or ID if needed. Create another task first if none is available.
3. Choose **Start**. The new Room opens while Cockpit calls the selected tasks.

The current task joins as host and wakes for every message. Called tasks default to waking only when a message `@mentions` their Room display name. Starting a Room does not create new agent tasks or change their agent, model, or plan requirements; each called task must be resumable and available.

Call status is separate from Room creation. If a task has not answered yet, delivery can still complete in the background; wait and check participation before calling it again. If a call fails, inspect the reason and use **Retry** for that task. A failed call does not discard the Room or participants that already joined.

## Create and show a Room

```bash
cockpit talk create --topic "API design review"
cockpit side-panel talk <room-id>
```

The creator joins automatically and wakes for every message by default. Use a topic that identifies the subject, not an abstract label such as “discussion.”

The Room can appear in the task's right side panel. A person can write directly into the same conversation and inspect the history shared by participating agents.

Each message normally shows its time. On Desktop, hover over a message or move keyboard focus to it to switch to the full sent date and time. In the PWA, tap a message to show the full timestamp and tap elsewhere to return to the normal display.

## Call an agent

Create the participating task first, then pass its task id to `call`.

```bash
cockpit talk call <room-id> <task-id>
```

`call` can revive a stopped or completed resumable task. An agent added later defaults to `mentions`. Add `--notify all` to make the agent an always-on participant that wakes for every message.

```bash
cockpit talk call <room-id> <task-id> --notify all
```

If the bounded wait returns `call_timeout`, delivery may still continue in the background. Do not immediately duplicate the call. Inspect participation through `talk get`.

## Identify the speaker explicitly

Pass `--task-id` to `say`, `join`, `leave`, and `mute`. A directory cannot uniquely identify the speaker when several tasks share it.

```bash
cockpit talk join <room-id> --task-id <my-task-id> --name "Reviewer"
cockpit talk say <room-id> --task-id <my-task-id> --text "I will review the change boundary"
```

Choose a short display name that distinguishes the participant's role. That name becomes the target of an `@mention`.

## Choose a notification mode

| Mode | Wakes when | Best for |
| --- | --- | --- |
| `all` | Every new message from another participant | Moderator or full-participation discussion |
| `mentions` | A message names the participant with `@DisplayName` | Specialist consulted only when needed |
| `none` | Normally never; an explicit mention still wakes it | Task that only posts results |

The creator defaults to `all`; an agent added later defaults to `mentions`. Notification controls when a finished agent task is resumed, not whether it may speak. An agent currently blocked in `wait` can receive new messages regardless of its notification mode.

```bash
cockpit talk mute <room-id> --task-id <task-id> --notify mentions
```

## Select a participant with a mention

To wake a participant using `mentions` or `none`, write its complete Room display name.

```bash
cockpit talk say <room-id> \
  --task-id <my-task-id> \
  --text "@Reviewer check compatibility"
```

A participant that left the Room does not return through a mention. Use `talk call` to bring it back. The `wake.notified` and `wake.skipped` fields show who resumed and who remained busy, muted, or unmentioned.

When `talk say` leaves a participant asleep because it uses `mentions` and was not mentioned, the response includes a `hint` naming that participant and explaining how to reach it. Mention its complete display name in your next message, or change it to an always-on participant with the command below. The hint does not change notification modes automatically.

```bash
cockpit talk mute <room-id> --task-id <task-id> --notify all
```

## Wait for the conversation

```bash
cockpit talk get <room-id>
cockpit talk wait <room-id> --since <latest-seq>
```

`talk get` reads the current Room, while `talk wait` blocks for messages after the supplied `seq`. A timeout is not a Room failure. Continue waiting from the same `--since`.

Send long text, Markdown, quotes, backticks, or `$` through `--stdin` or `--text-file`. Do not embed long content directly in a shell argument.

## Check delivery to each participant

A message's delivery summary shows per-recipient status in the room. CLI `talk get`, `log`, and `wait` expose `receipts` for participants present when the message was posted, excluding the author. Later membership or name changes do not rewrite those recipients.

- `sending`: delivery to the task runtime is pending.
- `received`: the runtime accepted the message, or the recipient fetched it through a task-context `get` or `wait`.
- `unconfirmed`: delivery is not confirmed, for example while the task is busy or after an interrupted delivery.
- `not_notified`: notification mode or mention rules excluded the recipient.
- `undeliverable`: delivery failed, for example because the task is unavailable or usage-limited.

**Received is not proof that the agent understood, acted on, or completed the request.** Reading the room as a person does not mark messages received by an agent. Older messages without receipts have unknown status, not failed delivery. See the [Talk reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/talk) for reason codes.

## Close, leave, and reopen

| Action | Result |
| --- | --- |
| `talk leave` | The calling participant leaves and stops receiving notifications |
| `talk close` | Nobody can speak, while the history remains |
| `talk reopen` | Conversation resumes with the same Room, participants, history, and sequence |

After discussion, explicitly hand the decision, unresolved issues, and next owner to a normal task or Fleet. Closing the Room does not itself complete implementation, verification, or publication.

See the [`cockpit talk` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/talk) for every command, [Task management (CLI)](https://agi-labo.com/en/tools/cockpit/docs/task-management) for one delegated task, and [Fleet](https://agi-labo.com/en/tools/cockpit/docs/fleet) for a fixed workflow.
