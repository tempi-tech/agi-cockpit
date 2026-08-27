<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Orchestration

Learn how to coordinate parent and child tasks, the Master Agent, task run and reports, Fleets, templates, and Talk Rooms.

> Verified with AGI Cockpit 4.61.0 on 2026-08-27. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/orchestration)

Orchestration divides one objective into independent tasks and explicitly collects their results for the next decision. It differs from Autorun, which starts work on a schedule, and Ask, which waits for human judgment.

## Split work across tasks

Run work in parallel only when it cannot corrupt shared files or decisions. Tasks using the same workspace can edit the same files, so assign separate scopes or isolate them with Git Worktrees.

Give each task an objective, scope, completion criteria, and required evidence. A request that names the code to inspect, verification to run, and report format is easier for the parent to compare than a vague request to investigate.

## Parent and child tasks

Parent-child relationships show task hierarchy in the task list and child tasks panel. Completing a child does not automatically send all of its results to the parent or resume the parent. Open the child, inspect its conversation and artifacts, and explicitly retrieve the needed result.

Dedicated child reports are shown separately from ordinary user messages and messages sent by another task. A report can contain a status summary and technical metadata. Verify requested evidence such as diffs, tests, and artifacts instead of relying on the displayed completion state.

## Use task run and reports

Use `cockpit task run` when an AI agent should start another task and wait for its first report. The returned `report` includes status, waiting reason, an increasing `seq`, and the latest message.

To wait for another report, pass the processed sequence to `cockpit task wait <id> --since <seq>`. To send follow-up instructions and wait for that turn's report, use `cockpit task send <id> --text "..." --wait`. A timeout does not mean that the task failed. Inspect its state, then continue waiting from the same sequence.

Do not use hierarchy as a substitute for report delivery. See the [`cockpit task` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/task) for the exact result contract.

## Use the Master Agent

The Master Agent is a dedicated task for breaking down work across projects and supervising progress through Cockpit task controls. Like any task, its scope and authorization for external action come from the user's instructions.

Provide the final objective, priorities, repositories, prohibited actions, and completion criteria. Interim reports are not proof of completion. Collect every required child result and reconcile dependencies and unresolved work before making the final determination.

## Use templates

Official and personal templates store repeatable task instructions and input variables. A template does not lock the runtime agent, workspace, or permissions. Review the expanded instruction and execution settings on the creation screen.

The CLI manages templates through `cockpit templates`. Its commands are documented in the [`cockpit settings` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/settings).

## Declare dependencies with Fleet

Fleet defines a dependency-aware Run of multiple task nodes in YAML and manages parallel execution, gates, retries, and resume. It is intended for repeatable workflows with dependency order, concurrency, approval points, and rerun rules, not for a single simple child task.

A Run can have a title, and a Fleet group shows progress for each node. Per-node notifications are suppressed and summarized at the end of the Run. Transient command-gate failures retry within configured limits. A saved Run can also recover after a Pull Request was merged manually.

See the [`cockpit fleet` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/fleet) for YAML, gates, operations, and recovery.

## Converse in Talk Rooms

A Talk Room is a shared conversation for multiple agents and people. Unlike one-way instructions through `task send`, the room history is the source of truth for the discussion.

Every new message wakes the creator. An agent added later wakes only for its own `@mention` by default. Use `notify all` when everyone must react to every message. Use `mentions` or `none` to avoid unnecessary resumes, and address the intended participant by display name.

A closed Room retains its history but cannot receive messages until reopened. See the [`cockpit talk` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/talk) for participation, notifications, waiting, and reopening.

## Separate Ask from Autorun

[Ask](https://agi-labo.com/en/tools/cockpit/docs/ask) receives a human decision and resumes the originating task. [Autorun](https://agi-labo.com/en/tools/cockpit/docs/autorun) starts a new task or sends instructions to an existing one at a time, interval, or cron schedule. Neither automatically integrates results from multiple tasks.

Choose Ask for a decision, Autorun for start time, parent-child tasks or Fleet for dependent work, and a Talk Room for a multi-participant discussion.

## Determine completion

Completion of an orchestrated workflow requires evidence that:

- Required child tasks or Fleet nodes reached a terminal state.
- Each result was retrieved and integrated in dependency order.
- No failure, skip, timeout, or unanswered Ask remains unresolved.
- Final acceptance criteria such as diffs, tests, or publication state were verified.
- Needed history was saved before deleting Rooms, Runs, or tasks.

See [Results and tools](https://agi-labo.com/en/tools/cockpit/docs/results-and-tools) for review surfaces and [Security and data](https://agi-labo.com/en/tools/cockpit/docs/security-and-data) for permission and deletion boundaries.
