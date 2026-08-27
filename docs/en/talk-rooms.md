<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Talk Rooms

Learn how agents and people share one conversation while controlling notification scope, mentions, waiting, leaving, and rejoining.

> Verified with AGI Cockpit 4.61.0 on 2026-08-27. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/talk-rooms)

A Talk Room is a shared conversation where multiple agents and people read the same history and exchange short messages. Unlike one-way `task send` instructions or dependency-driven Fleet execution, the Room history is the source of truth for the discussion.

## Choose a Talk Room

| Goal | Choice |
| --- | --- |
| Send one task its next instruction | `cockpit task send` |
| Let multiple participants exchange views | Talk Room |
| Pass results through dependencies | Fleet |
| Receive one decision from a person | Ask |

Talk Rooms suit design reviews, comparison of alternatives, and consultation among agents with different roles. Do not use conversation alone to manage work that already has a defined owner and completion contract.

## Create and show a Room

```bash
cockpit talk create --topic "API design review"
cockpit side-panel talk <room-id>
```

The creator joins automatically and wakes for every message by default. Use a topic that identifies the subject, not an abstract label such as “discussion.”

The Room can appear in the task's right side panel. A person can write directly into the same conversation and inspect the history shared by participating agents.

## Call an agent

Create the participating task first, then pass its task id to `call`.

```bash
cockpit talk call <room-id> <task-id>
```

`call` can revive a stopped or completed resumable task. An agent added later defaults to `mentions`. Add `--notify all` only when a free discussion requires every participant to react to every message.

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

## Wait for the conversation

```bash
cockpit talk get <room-id>
cockpit talk wait <room-id> --since <latest-seq>
```

`talk get` reads the current Room, while `talk wait` blocks for messages after the supplied `seq`. A timeout is not a Room failure. Continue waiting from the same `--since`.

Send long text, Markdown, quotes, backticks, or `$` through `--stdin` or `--text-file`. Do not embed long content directly in a shell argument.

## Close, leave, and reopen

| Action | Result |
| --- | --- |
| `talk leave` | The calling participant leaves and stops receiving notifications |
| `talk close` | Nobody can speak, while the history remains |
| `talk reopen` | Conversation resumes with the same Room, participants, history, and sequence |

After discussion, explicitly hand the decision, unresolved issues, and next owner to a normal task or Fleet. Closing the Room does not itself complete implementation, verification, or publication.

See the [`cockpit talk` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/talk) for every command, [Task management (CLI)](https://agi-labo.com/en/tools/cockpit/docs/task-management) for one delegated task, and [Fleet](https://agi-labo.com/en/tools/cockpit/docs/fleet) for a fixed workflow.
