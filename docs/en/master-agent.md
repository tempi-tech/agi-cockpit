<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Master Agent

Learn how to give a Master Agent a broad objective and supervise its task decomposition, progress tracking, result integration, and recurring operation.

> Verified with AGI Cockpit 4.61.0 on 2026-08-27. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/master-agent)

The Master Agent is a dedicated task that divides a broad objective into units of work, starts and supervises Cockpit tasks, and combines their results into one decision. It normally delegates implementation to suitable agent tasks and focuses on assignment, waiting, review, and integration.

## Distinguish a normal task, Master Agent, and Fleet

| Choice | Best for |
| --- | --- |
| Normal task | One clear result from one agent |
| Master Agent | Deciding how to divide an objective and adjusting assignments or priorities while work progresses |
| Fleet | Fixing known dependencies in YAML so the same workflow can be rerun and recovered |

Choose Fleet when the procedure is stable. Choose the Master Agent when decomposition itself requires judgment. [Task management (CLI)](https://agi-labo.com/en/tools/cockpit/docs/task-management) is enough for one delegated task.

## Create a Master Agent

Choose Master Agent in the Desktop or PWA creation screen, then enter its name and initial instruction. It runs in a Cockpit-managed Master directory rather than an ordinary project directory. Multiple Master Agents can run at once, each with its own conversation and child tasks.

To start one on a schedule through the CLI, add `--master` to an Autorun.

```bash
cockpit autorun create \
  --name "Periodic review" \
  --instruction "Inspect incomplete tasks and report only important stalls" \
  --master \
  --type interval \
  --minutes 60
```

Autorun controls when work starts. How the Master Agent decomposes and supervises work after launch is a separate responsibility.

## Write the initial instruction

Give the Master Agent clear decision boundaries instead of attempting to prescribe every step in advance.

- Final objective and observable completion state.
- Target projects or workspaces.
- Priorities, deadlines, and work that may run concurrently.
- Areas that must not be changed.
- Publication, push, deletion, or sending that requires prior approval.
- Diffs, tests, URLs, and open issues required in the final report.

For example, do not say only “fix the bug.” Include reproduction conditions, target repository, excluded areas, verification, and whether publication requires a separate approval.

## Supervise the decomposition

Tasks created by the Master Agent appear in a parent-child hierarchy. The child tasks panel opens each task's state, conversation, report, and artifacts.

Do not treat the number of tasks as progress. Confirm that:

- Tasks that edit the same files are not carelessly running in parallel.
- Every task has a scope and completion criteria.
- Implementation that depends on investigation starts in dependency order.
- Waiting or usage-limited tasks receive the next required decision.
- Unneeded tasks are not completed or removed without authorization.

Hierarchy alone does not integrate results. The Master Agent uses `task run`, `task wait`, and `task get` to collect reports, then opens child artifacts when direct verification is needed.

## Change direction during the work

Send follow-up instructions through the same conversation. Propagate a new constraint explicitly to every affected child. Telling only the Master Agent does not guarantee that a child already running has received the new boundary.

For a substantial change, state:

1. What changed.
2. Which work may continue.
3. Which work should stop or be discarded.
4. When existing results remain reusable.
5. The new completion criteria.

## Manage memory

`memory.md` in the Master directory is a concise note shared across Master Agent sessions. Store only policies the user explicitly asked to remember or repeatedly confirmed. Do not store guesses, temporary conditions, or secrets. Remove outdated items and keep the file short.

## Control permissions and external actions

Being a Master Agent grants no broader authority than an ordinary task. Creating a child, sending data to an external site, pushing a repository, publishing, and deleting data are separate actions.

When the initial instruction did not authorize publication, stop after preparation and verification, then ask immediately before the externally visible action. Also distinguish an agent's technical tool-permission prompt from the user's authorization for the underlying operation.

## Determine completion

A Master Agent is not complete merely because every child appears idle. Confirm that:

- Required child reports and artifacts were collected.
- Dependent results were integrated in the correct order.
- Failures, confirmations, usage limits, and unanswered Asks were resolved or clearly accounted for.
- Final criteria such as diffs, tests, builds, UI state, or publication state were verified.
- Rejected alternatives and remaining constraints appear in the final report.

See [Task management (CLI)](https://agi-labo.com/en/tools/cockpit/docs/task-management) for supervising one child, [Fleet](https://agi-labo.com/en/tools/cockpit/docs/fleet) for a fixed dependency graph, and [Talk Rooms](https://agi-labo.com/en/tools/cockpit/docs/talk-rooms) for multi-agent discussion.
