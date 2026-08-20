<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Autorun

Learn how Autorun starts a new task or sends an instruction to an existing task from a one-time, interval, or cron schedule.

> Verified with AGI Cockpit 4.56.0 on 2026-08-20. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/autorun)

Autorun starts a new task or sends an instruction to an existing task at a specified time, interval, or cron schedule. It does not coordinate several agents inside one run. It is an independent way to start or continue the same kind of work when it is needed.

## Requirements

Autorun requires an AGI Labo membership. Cockpit verifies membership not only at the Desktop entry point, but also in the PWA, `cockpit autorun`, the local API, and each scheduled execution. Authentication that needs renewal, an inactive membership, and a temporary verification failure produce distinct errors during an operation. Cockpit rejects an operation safely when a network or API failure prevents verification, but it does not cache that failure and block a member after service recovers. Existing members can use the CLI while signed in to AGI Labo in Desktop.

Schedules run inside the AGI Cockpit app process. Closing every window on macOS does not stop the scheduler. The operating-system scheduler does not launch Cockpit after the process exits, but Cockpit performs one catch-up per Autorun when it restarts or resumes within 24 hours of an unhandled scheduled time. Older occurrences are recorded as missed instead of silently starting stale work.

## Schedule types

| Type | Setting | Behavior |
| --- | --- | --- |
| Once (`once`) | Date and time | Starts once, then disables itself |
| Interval (`interval`) | Minutes, hours, or days | Starts after the selected interval from the previous scheduling pass |
| Cron (`cron`) | `minute hour day month weekday` | Starts at the next time that matches the cron expression |

In the cron weekday field, `0` means Sunday. For example, `0 9 * * 1-5` runs at 9:00 a.m. on weekdays.

## Create an Autorun

1. Open the app menu in the lower-left corner of Desktop, then select **Autorun tasks**.
2. Select **New Autorun**.
3. Enter a name and the instruction to send when the task starts.
4. Under **Execution target**, choose **Create a new task** or **Send to an existing task**.
5. For a new task, choose the working directory and agent, then review any shown UI mode, account, model, reasoning effort, service tier, system prompt, and approval mode. Cockpit uses the Master's working location for Master Agent runs, and **Auto** is the default for agents with account selection.
6. For an existing task, choose **Target task**. Cockpit creates no new runtime; it uses the target's existing agent, conversation, working directory, and runtime settings.
7. Choose **Once**, **Interval**, or **Cron** and configure the timing.
8. Save the Autorun and confirm that the list shows its next run time.

An Autorun that creates a new task keeps its runtime settings from the time it is saved as a snapshot. Changing global settings later does not silently change its model, reasoning effort, service tier, system prompt, approval mode, account-selection method, or UI mode. If a pinned model or account becomes unavailable, Cockpit does not substitute another setting. It disables that Autorun and marks it as needing attention.

A new-task Autorun also keeps its Browser Identity assignment and passes it to every task the schedule creates. An Autorun without an explicit assignment uses the Default Identity. In v4.43.0, assign or change an Autorun's Identity from the CLI. Create, rename, recolor, clear, or remove the Identity itself from **Browser Identity** in the lower-left app menu or from the CLI. An existing-task Autorun does not change the Browser Identity already assigned to its target.

For a new task, Desktop and the PWA show only settings supported by the selected agent and UI mode. The CLI and API use the same capability data and reject unsupported combinations with an explicit error before saving.

Cursor Autoruns can use **Native UI** or **Terminal**. Native UI exposes Cursor's available model, approval mode, and account profiles in Desktop, the PWA, and CLI-backed creation. Cursor does not expose reasoning-effort, service-tier, or system-prompt settings.

Qoder Autoruns can also use **Native UI** or **Terminal**. Native UI exposes Qoder's available model, system prompt, approval mode, and account profiles in Desktop, the PWA, and CLI-backed creation. Qoder does not expose reasoning-effort or service-tier settings.

Claude, Codex, Grok Build, Cursor, and Qoder Autoruns store either **Auto** or a pinned account. Auto is the default for new Autoruns. It is stored as a selection method rather than as one concrete profile, so every execution chooses again from the usage state of signed-in accounts. If the created task reaches a usage limit or returns a plan-restriction response, Cockpit also switches to another available account and continues processing. Use `--account <name|id|default>` to pin an account from the CLI and `--account auto` to select Auto.

## Review a run

When a new-task Autorun fires, it creates a task in the same form as any regular task. Select it from the task list, then use task details to review its conversation, confirmation requests, errors, and results. The new task keeps a reference to the Autorun ID that created it.

An existing-task Autorun sends its text as that task's next instruction. If the target can accept input, delivery happens immediately. If it is running or stopped by a usage limit, Cockpit stores the instruction in a persistent main-process queue until the task becomes ready. The queue has no timeout, and completed or errored targets are resumed automatically when possible. Delivery fails, disables the Autorun, and marks it as needing attention if the target is deleted, cannot be resumed, or is a Windows Terminal task whose foreground process cannot be verified safely.

The Autorun manager supports:

- enable and disable
- edit
- delete
- **Run Now**
- previous and next run times
- the latest run status and the created or target task ID

Every scheduled occurrence records one of `created`, `queued`, `delivered`, `skipped-membership`, `skipped-lock`, `skipped-period`, `missed`, or `failed`. `queued` means an existing-task instruction is waiting; `delivered` means it was sent. Authentication or membership skips, missed runs, and startup or delivery failures are marked as needing attention in Desktop and the PWA and produce a desktop notification. Overlap and period-dedup skips remain informational. `cockpit autorun list` and `cockpit autorun get` expose the same `lastRunStatus`, `lastRunScheduledAt`, `lastRunDetail`, and `lastTaskId` fields, plus `executionTarget` and `targetTaskId`. `nextRunAt` is the next attempt time, while `nextRunScheduledAt` preserves the original occurrence during retries. Cockpit also appends one JSON line per occurrence to `data/cockpit/logs/autorun-executions.jsonl` under the AGI Tools data directory. A queued occurrence receives a later delivery or failure record. Repeated retries with the same outcome are recorded once; the log rotates at 5 MB and keeps two archives.

For a new-task Autorun, Cockpit skips a scheduled run only while another launch is still starting. The lock covers the launch itself and never waits for the created task to finish, so the next occurrence creates a new task even while the previous one is still running or waiting for confirmation. A 24-hour expiry is the final safeguard against a launch that never settles. Existing-task delivery releases the lock the same way once the occurrence is queued or delivered; pending instructions are processed in order for each target task.

## Working directory and failures

For a new task, the Desktop form requires a directory unless the Autorun runs as the Master Agent. If the CLI omits the directory, Cockpit creates an `agi-cockpit` directory inside the operating-system temporary folder. Existing-task delivery uses the target's working directory and does not take a separate Autorun directory.

If the agent fails to start, the task remains in the task list with an **Error** state. A one-time Autorun still becomes disabled; an interval or cron Autorun remains enabled and calculates its next run. Open the created task details to inspect the cause.

If Cockpit confirms that the account is a guest or has an inactive membership when a run is due, a one-time Autorun does not start and becomes disabled. An interval or cron Autorun remains enabled and checks again at its next scheduled time. If the sign-in session needs renewal or a temporary network or API failure prevents membership verification, Cockpit does not start the run and checks again one minute later. It keeps a one-time Autorun enabled and persists the retry time, so authentication renewal or a temporary outage does not consume its only run.

## Create from the CLI

```bash
cockpit autorun create \
  --name "Weekday progress review" \
  --instruction "Review the unfinished work in this project and summarize it in priority order." \
  --directory /path/to/project \
  --agent-type codex \
  --account auto \
  --ui-mode visual \
  --model gpt-5.4 \
  --effort high \
  --service-tier fast \
  --approval-mode accept-edits \
  --browser-identity work \
  --type cron \
  --expression "0 9 * * 1-5"
```

```bash
cockpit autorun list
cockpit autorun get <id>
cockpit autorun run <id>
cockpit autorun toggle <id>
cockpit autorun update <id> --browser-identity default
```

To send to an existing task, specify the execution target and task ID:

```bash
cockpit autorun create \
  --name "Continue review" \
  --instruction "Check progress and continue with the next step." \
  --execution-target existing-task \
  --target-task <task-id> \
  --type interval \
  --minutes 30
```

## Related pages

- [Task list and task details](https://agi-labo.com/en/tools/cockpit/docs/tasks)
- [Ask](https://agi-labo.com/en/tools/cockpit/docs/ask)
