<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Task management (CLI)

Learn how to create and delegate Cockpit tasks, inspect state and reports, send follow-ups, resume work, and finish tasks safely through the CLI.

> Verified with AGI Cockpit 4.71.0 on 2026-09-05. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/task-management)

`cockpit task` lets an AI agent or person create Cockpit tasks, read their state, send the next instruction, and collect results. Use this flow to delegate one job to another task. Use [Fleet](https://agi-labo.com/en/tools/cockpit/docs/fleet) when a reusable YAML workflow needs dependency order.

## Choose create or run

| Goal | Command | Return behavior |
| --- | --- | --- |
| Create a task and receive its id | `cockpit task create` | Returns immediately after creation |
| Create a task and wait for its first report | `cockpit task run` | Returns at the first stopping point, error, or completion |
| Run only a shell command | `cockpit task create --agent-type terminal --command "..."` | Creates a Terminal task |

Use `run` when the child result is required for the next decision. Use `create` when work can continue independently and you will inspect it later.

```bash
cockpit task run \
  --instruction "Investigate the change and report the cause and evidence" \
  --directory /path/to/repo \
  --agent-type codex
```

Pass long instructions, Markdown, quotes, backticks, or `$` through `--stdin` or `--instruction-file` instead of embedding them in a shell argument.

```bash
cockpit task run --instruction-file instruction.md \
  --directory /path/to/repo \
  --agent-type codex
```

After `create` succeeds, it returns the new `taskId` and the account Cockpit actually selected. If `run` creates the task but times out or encounters a wait error before receiving the first report, it still returns `ok: true` with `data.taskId`. The task already exists in that case, so do not submit the same instruction again; continue with `task get` or `task wait` for the returned id. `ok: false` means task creation itself failed.

## When the creation response is lost

`task create` and `task run` send a unique create key with every creation. If the connection drops after sending the request, the CLI first looks up a task already created with that key. A match returns the same `taskId` with `data.replayed: true`, preventing a lost acknowledgement from creating a duplicate. The CLI retries creation with the same key only after Cockpit confirms that no task was created.

`task_create_interrupted` means Cockpit confirmed that no task exists, so the same command can be run again. `task_create_outcome_unknown` means the outcome could not be confirmed and the task may exist. Inspect `cockpit task list` before creating another task in that case. The running Cockpit retains create-key records for ten minutes; after the app restarts, the same key can no longer reconcile a surviving task.

The CLI normally generates a UUID. Use `--create-key` only when an external workflow needs a stable correlation value. It accepts 1–128 letters, digits, `.`, `_`, `:`, and `-`. Reusing the key from the same caller returns the same task.

```bash
cockpit task create \
  --instruction "Verify the publication state" \
  --directory /path/to/repo \
  --create-key deploy-2026-09-05-1
```

## Set the workspace and runtime

Specify `--directory` for an existing project. Without it, a task starts in an operating-system temporary directory that may be removed on completion. Use `--worktree` when the task needs an isolated Git Worktree.

The agent, model, reasoning effort, account, approval mode, and Browser Identity are task runtime settings. Unsupported combinations fail instead of silently selecting a different setting. For work on an external site, explicitly assign the Browser Identity that holds the required sign-in state.

```bash
cockpit task create \
  --instruction "Verify the publication state in the admin console" \
  --directory /path/to/repo \
  --agent-type codex \
  --browser-identity work
```

## Create parent and child tasks

A task created from another task is a child of the caller by default. Use `--parent-task-id` to name another parent. The hierarchy groups work in the task list and child tasks panel, but it is not a report-delivery contract.

When a child reaches a stopping point, the parent does not necessarily receive every artifact automatically. The parent must read the return from `task run`, call `task wait` or `task get`, and verify requested evidence such as diffs, tests, or URLs.

## Read state and waiting reasons

```bash
cockpit task list
cockpit task list --status waiting_confirmation
cockpit task get <task-id> --turns 3 --max-lines 500
```

| Field | Decision |
| --- | --- |
| `running` | Work is active. Wait unless a follow-up is genuinely needed |
| `waiting_confirmation` | Input is required. Read `waitingReason` and `readyForNextPrompt` |
| `completed` | The process ended. This does not prove that the requested result was verified |
| `error` | Startup or execution failed. Read `errorMessage` and recent conversation |
| `needsResume: true` | The process stopped and the same task can be resumed |

If `waitingReason` is `permission` or `question`, an agent-side confirmation is still active. Do not layer another instruction while `readyForNextPrompt` is false. For `usage_limit`, inspect available accounts and the reset time.

## Receive reports in order

`report.seq` increases per task in results from `task run` and `task wait`. Pass the last processed sequence through `--since` to wait for the next report without processing the same one twice.

```bash
cockpit task wait <task-id> --since <last-seq> --timeout 110
```

`timeout: true` does not mean the task failed. Inspect current state when needed, then continue from the same `--since`. Use `task wait` for continued monitoring instead of repeatedly polling `task get` at short intervals.

## Send a follow-up

Use `task send` after the task can accept another instruction. Add `--wait` when the next report is required in the same control flow.

```bash
cockpit task send <task-id> --text "Fix only the failing test and run it again" --wait
```

Use `--stdin` or `--text-file` for multiline content. To send only Enter to an agent confirmation, run `cockpit task send <task-id>` with no text. Read `waitingReason` first so you know whether you are answering a question, approving a tool, or sending an ordinary follow-up.

## Switch the account or Browser Identity

Supported agents can switch a running task to another signed-in account after a usage limit. Send the appropriate continuation after the switch.

```bash
cockpit task account <task-id> work
```

Changing Browser Identity selects the persistent browser partition for the task's next browser session. It does not copy cookies or localStorage between Identities.

```bash
cockpit task browser-identity <task-id> work
```

## Distinguish completion, resume, and removal

| Action | Result |
| --- | --- |
| `task resume` | Restarts a stopped task with the same history |
| `task complete` | Stops the process and moves it to completed. The CLI removes its Worktree by default |
| `task complete --keep-worktree` | Moves it to completed while retaining the Worktree |
| `task remove` | Deletes the task and its Cockpit history |

Complete or remove a task only when the user explicitly requests it. Save required diffs, artifacts, reports, and publication URLs, then verify the target id before acting.

## Determine completion

Task management is complete only after confirming that:

- Requested results and evidence were collected from the report or actual files.
- No confirmation, usage limit, error, or timeout remains unresolved.
- The parent integrated child results in the required order.
- External publication or deletion stayed within the approved target.
- Needed changes were saved before a workspace was removed.

See the [`cockpit task` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/task) for every option, and [Task list](https://agi-labo.com/en/tools/cockpit/docs/tasks) plus [Task details](https://agi-labo.com/en/tools/cockpit/docs/task-details) for the corresponding UI.
