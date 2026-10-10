<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Task list

Understand projects, task grouping and movement, the task list, Overview, search, states, completion, and deletion.

> Verified with AGI Cockpit 4.104.0 on 2026-10-11. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/tasks)

The task list is where you choose which piece of work to inspect next. Use [Task details](https://agi-labo.com/en/tools/cockpit/docs/task-details) for its conversation and follow-up input.

## Desktop columns and Overview

Desktop places the task list, the selected task's work area, and a contextual panel side by side. The task list can be resized or collapsed, and closing the panel gives the work area more room.

| Area | Primary role |
| --- | --- |
| Task list | Shows active work and sorts it by priority, status, creation time, or update time |
| Work area | Shows the selected task's conversation, progress, confirmation requests, and composer |
| Contextual panel | Shows files, diffs, the browser, App Surface, terminals, and other supporting surfaces |

Overview searches across tasks, projects, and agents, including completed work. Select the **Running**, **Waiting**, **Completed**, or **Error** status badge at the top to move matching tasks ahead of the others while preserving their project sections. This prioritizes rather than filters: tasks in other states remain below them. Select the active badge again to restore the usual order.

The header's Back and Forward buttons remain available while Overview is open. Navigating to a task from that history closes Overview and opens the task. In the task list, filter by agent and pin a task or project. Switching the selected task does not stop the other agents; each continues independently.

### Open and operate tasks on another PC

The **Device** filter in the Desktop task list shows only **This device** by default. Choose **All devices** or one device to inspect task summaries from another PC that uses the same Tailscale account and has Remote Access enabled. Cockpit must also be running on the other PC.

Remote rows show the task name, instruction preview, state, pending Ask, agent, working folder, and time, and follow the current search, sort order, and agent filter. Select a row to open that device's PWA task detail in the Desktop work area. Confirm the displayed device name before reading the conversation, sending a message, answering an Ask, or stopping or completing the task. The remote row itself has no action menu.

An open remote task follows navigation to another task on the same device and Desktop Back and Forward history. If synchronization drops briefly, Cockpit keeps the loaded view for a short reconnection period before replacing it with the offline state. Separate states and recovery actions identify an offline or missing device, a removed task, and an unresponsive destination. A device that rejects the connection because it runs an older version or has a different Tailscale owner is shown as unavailable.

Switch back to **This device** to close an open remote task, stop discovering and connecting to other PCs, and show only local tasks. This feature requires an AGI Labo membership and Tailscale. See [Security and data](https://agi-labo.com/en/tools/cockpit/docs/security-and-data#protect-remote-access) for the difference between the list and task-control connections, and [Remote access](https://agi-labo.com/en/tools/cockpit/docs/remote-access) for setup.

## Search, sort, and use menus

For tasks on this device, task-list search partially matches displayed task and project names. A task ID becomes searchable after at least four characters. It does not inspect a local task's instruction, working directory, or internal metadata. When another device is visible, search also partially matches the task name, instruction preview, and working folder received from that device, but not its remote task ID.

The **Pinned** heading on Desktop and the PWA shows the total number of unfinished pinned tasks. When search or the agent filter narrows the list, it shows **visible / total**; without filtering, it shows the total. The total remains visible when the group is collapsed or no pinned task matches the filter.

In the PWA, select a project heading to collapse or expand that project's tasks. A collapsed heading keeps the task count and running or unread indicators visible. The choice is stored in that browser for each project and survives a reload, but it does not sync to other devices. Search can hide groups that do not match without changing their saved collapsed state. A new or reintroduced project starts expanded; completed-only sections and the Pinned group keep their existing behavior.

In the PWA task list, a question-bubble marker labelled **Waiting for an Ask answer** appears while that task has an unanswered Ask. It follows the actual open Ask rather than inferring from `waitingReason: question`, so it clears after the Ask is answered or closed. Selecting the row still opens the task; open **Confirm** to answer the Ask. A session-unrecoverable warning or usage-limit warning takes display priority over the Ask marker because it identifies a separate recovery blocker.

On Desktop, Command/Ctrl+K opens a search palette across projects. In addition to task names, first instruction lines, and project names, it searches app destinations such as **New task**, **Settings**, **Agent settings**, **Ask notifications**, **Hooks**, **Display**, **Remote access**, **Autorun tasks**, **History**, **Fleet list**, **Official documentation**, and **Updates**. Results include completed tasks. Use the arrow keys to select, Enter to open, and Escape to close. With an empty query, the palette shows recent tasks and primary destinations. The default key can be changed in Shortcut settings.

The **…** menu on Desktop task rows and child-task entries, and on PWA task rows, supports rename, pin or unpin, move to another project, complete, copy task ID, and delete. A long press on a PWA task row opens the same menu. Moving a task also moves its child tasks. Master Agent and Creative Studio tasks cannot be moved into projects. A child can also be detached from its parent. Confirm a name with Enter or **Save**, and cancel with Escape or **Cancel**. An empty name is not saved, and the limit is 50 characters. While the PWA list refreshes, an open dialog keeps its draft.

The project-heading menu can complete that project's unfinished tasks or delete all of its tasks in one operation. Bulk completion includes running tasks and Fleet tasks awaiting confirmation; the native confirmation lists these counts before you proceed. Bulk deletion removes every task in the project, including running tasks, not only completed ones. The confirmation names how many running tasks will be stopped and how many associated Fleet runs will be stopped if they are active. This cannot be undone.

Automatic sorting does not move rows while the pointer is over the list or while a menu, rename, or deletion confirmation is active. The current order is applied after the interaction ends.

Groups with many tasks initially show a limited count. **Show more** reveals additional entries in steps; after the first expansion, **Collapse** returns to the initial count on both Desktop and PWA.

In the Desktop sidebar, after you select a parent task that has children, selecting that same parent again reveals all of its children; selecting it once more returns to the initial count. When another task is open, the first selection only navigates to the parent.

### Continue a search in the PWA

PWA task search saves the current query in the device's browser separately for each connected host. Returning from task details or reopening the screen restores the query and its filtering.

Select the search field to open **Recent searches**, with up to five entries you can reuse. While typing, history is filtered by prefix. Confirming with Enter or the keyboard's search key, or opening a task from the results, adds that query to history.

**Clear search** removes only the current query. Use **Remove from history** for one entry or **Clear history** for all entries; neither changes the current query. Storage is local to that browser and does not sync to other devices. If browser storage is unavailable, search still works within the screen, but cannot be restored after reopening.

## Continue recent Claude Code and Codex sessions

When the task board is empty, select **Continue where you left off** to scan up to 200 Claude Code and Codex sessions from the last 30 days. Sessions are grouped by workspace and sorted by recent activity. Select sessions individually or by workspace, then import them together. Imported tasks resume in Terminal UI; the original session files are not changed and importing does not start an agent.

## Create and manage projects

A project has a name and one or more folders. The first folder is primary: new tasks start there unless you select another folder in the project. A folder can belong to more than one project, and projects can share a name. Create and edit projects from **Settings → Projects**, **Project details** in a project menu or New task picker, **Manage projects** in the PWA, or `cockpit project`.

Project details can rename the project, add or remove folders, reorder them, and change the primary folder. Changes save immediately. A running agent receives the current folder set when its next session starts; reconnect a supported Native UI task when it must pick up the change now. Deleting a project removes only the grouping: its folders remain and its tasks move to **No project**.

Projects group their tasks in Desktop, Overview, and the PWA. Tasks created in Cockpit-managed temporary or persistent folders remain grouped by folder until a project is explicitly selected or a persistent folder group is converted with **Make it a project**. Master Agent and Creative Studio tasks keep their own groups and do not belong to projects.

Desktop's sidebar and overview board, and the PWA project list, show a project-specific icon when one is available. Cockpit detects local project icons such as favicons; otherwise it uses the default folder appearance.

Use `cockpit project icon get --project <id|name>` to inspect the effective icon, `cockpit project icon set <image-path> --project <id|name>` to choose a local image, and `cockpit project icon reset --project <id|name>` to return to automatic detection. Icon changes also synchronize to connected PWA clients. No image is downloaded from the network. See [project reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/project) for folder rules, formats, and size limits.

## Task entry points and workspaces

A regular new task can use a project folder, a Cockpit-managed persistent or temporary folder, or a Git Worktree. For temporary work, choose whether to reuse an existing folder and its contents or create a new empty folder. Persistent folders live under `~/.agi-tools/workspaces`; a temporary folder is deleted on completion after no unfinished task still uses that location. A Worktree task remains assigned to the project of its source folder, and only that source folder is replaced by the Worktree.

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

The Fleet group's running indicator and status sort follow the state of the Run as a whole. Even when no agent task is temporarily running, a Run evaluating a command or human gate remains shown as running in Desktop and the PWA.

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

In the PWA, the completed group has a delete-all action with a native confirmation. Deletion cannot be undone. Long-press a Fleet group to access its run actions, including removal; review whether the chosen action removes only the run history or its tasks as well.
