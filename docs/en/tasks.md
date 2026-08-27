<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Task list

Understand the task list, Overview, search, sorting, workspaces, states, completion, and deletion.

> Verified with AGI Cockpit 4.62.0 on 2026-08-28. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/tasks)

The task list is where you choose which piece of work to inspect next. Use [Task details](https://agi-labo.com/en/tools/cockpit/docs/task-details) for its conversation and follow-up input.

## Desktop columns and Overview

Desktop places the task list, the selected task's work area, and a contextual panel side by side. The task list can be resized or collapsed, and closing the panel gives the work area more room.

| Area | Primary role |
| --- | --- |
| Task list | Shows active work and sorts it by priority, status, creation time, or update time |
| Work area | Shows the selected task's conversation, progress, confirmation requests, and composer |
| Contextual panel | Shows files, diffs, the browser, App Surface, terminals, and other supporting surfaces |

Overview searches across tasks, projects, and agents, including completed work. In the task list, filter by agent and pin a task or project. Switching the selected task does not stop the other agents; each continues independently.

## Search, sort, and use menus

Search inside the task list partially matches displayed task and project names. A task ID becomes searchable after at least four characters. This search does not inspect instructions, working directories, or internal metadata.

On Desktop, Command/Ctrl+K opens a task palette across projects. It searches task names, the first line of instructions, and project names, including completed tasks. Use the arrow keys to select, Enter to open, and Escape to close. The default key can be changed in Shortcut settings.

The **…** menu on a task row or child-task entry supports rename, pin or unpin, complete, copy task ID, and delete. A child can also be detached from its parent. Enter confirms an inline rename, Escape cancels it, an empty name is not saved, and the limit is 50 characters.

The project-heading menu can complete that project's unfinished tasks or delete its completed tasks in one operation. Running tasks and Fleet tasks awaiting confirmation are excluded from bulk completion by default and must be explicitly included. Bulk deletion applies only to completed ordinary tasks.

Automatic sorting does not move rows while the pointer is over the list or while a menu, rename, or deletion confirmation is active. The current order is applied after the interaction ends.

Groups with many tasks initially show a limited count. **Show more** reveals additional entries in steps; after the first expansion, **Collapse** returns to the initial count on both Desktop and PWA.

## Task entry points and workspaces

A regular new task can use a project, persistent directory, temporary directory, or Git worktree. Persistent directories live under `~/.agi-tools/workspaces`; temporary directories are deleted when their tasks are completed.

Quick Task opens a compact creation window from a global shortcut without leaving the current app. After creation, supervise it through the regular task list and task details.

A Git worktree isolates changes in another checkout, but the same branch cannot be checked out in multiple worktrees. Completing from Desktop preserves the worktree. `cockpit task complete <id>` deletes it by default and preserves it only with `--keep-worktree`.

## Read task states

| State | Meaning | What to check next |
| --- | --- | --- |
| Running (`running`) | An agent or command is processing | Read progress and interrupt only when needed |
| Awaiting confirmation (`waiting_confirmation`) | A response ended, a question is open, or permission is needed | Read the waiting reason and answer or decide |
| Completed (`completed`) | A person marked the whole task complete | Confirm required output was saved |
| Error (`error`) | Startup or execution could not begin | Read the reason in task details |

Awaiting confirmation distinguishes `turn_complete`, `permission`, `question`, `terminal_prompt`, `runtime_error`, `usage_limit`, `idle_timeout`, and `unknown`. `turn_complete` means one response ended; it does not mean the whole task is complete.

`needsResume` is not another state. It means an unfinished task lost its runtime process and must reconnect to a saved session.

## Distinguish Fleet Runs

A titled Fleet Run shows its title in the task-list Fleet group and Fleet details. Each node task is named **Run title / node name**, so the Run title is searchable like an ordinary task name.

**Delete Run** removes saved Run history but leaves related tasks. **Delete Run and all tasks** also removes every related task shown in the confirmation. The latter cannot be undone, so preserve required output first.

## Complete and delete

Complete moves a task from active work to completed work. Delete removes the task record from Cockpit. They are separate operations.

When saved tasks exist, AGI Cockpit does not overwrite them with an empty list unless you explicitly delete the final task. If `state.json` cannot be read at startup, Cockpit stops before writing and shows recovery steps using the data folder and adjacent `backups` directory.

## Inspect the list and state from the CLI

```bash
cockpit task list
cockpit task get <id>
cockpit task complete <id> --keep-worktree
```

`task get` returns `status`, `waitingReason`, `readyForNextPrompt`, and `needsResume`, together with the latest conversation and terminal output.

## Related pages

- [Task details](https://agi-labo.com/en/tools/cockpit/docs/task-details)
- [Results and tools](https://agi-labo.com/en/tools/cockpit/docs/results-and-tools)
- [Fleet](https://agi-labo.com/en/tools/cockpit/docs/fleet)
- [cockpit browser](https://agi-labo.com/en/tools/cockpit/docs/browser)
- [Security and data](https://agi-labo.com/en/tools/cockpit/docs/security-and-data)
