<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Task list and task details

Understand the task list, Overview, task details, task states, follow-up instructions, quotes, attachments, resume, and completion.

> Verified with AGI Cockpit 4.48.1 on 2026-08-09. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/tasks)

The task list is where you choose what to look at next. Task details is where you understand and act on the selected work. Overview searches across tasks, projects, and agents.

## Desktop three-column layout and Overview

The Desktop task screen places the task list, the selected task's work area, and an operation panel side by side. You can resize or collapse the task list and close the operation panel to give the work area more room.

| Area | Primary role |
| --- | --- |
| Task list | Shows active work and sorts it by priority, status, creation time, or update time |
| Work area | Shows the selected task's conversation, progress, confirmation requests, and composer |
| Operation panel | Shows settings, files, diffs, the browser, the terminal, or other content needed for the current action |

Overview is not one of the task-screen columns. It searches across tasks, projects, and agents, including completed work. The task list can filter by agent and pin a task or project. Switching the selected task does not stop the other agents; each task continues independently.

## Task states

| State | Meaning | What to check next |
| --- | --- | --- |
| Running (`running`) | An agent or command is processing | Read the progress. Interrupt only when the direction must change immediately |
| Awaiting confirmation (`waiting_confirmation`) | A response ended, a question is open, or permission is required | Check the waiting reason, then reply or return the requested decision |
| Completed (`completed`) | A person marked the whole task complete | Confirm that every result you need has been saved |
| Error (`error`) | The agent or command failed to start | Read the error in task details |

**Awaiting confirmation** does not have a single meaning. An AI agent or the `cockpit` CLI should also inspect the waiting reason before acting.

| Waiting reason | Meaning |
| --- | --- |
| `turn_complete` | One response ended and the task can accept the next instruction |
| `permission` | A tool action needs to be allowed or rejected |
| `question` | The running agent needs an answer to its own question |
| `terminal_prompt` | The terminal is waiting for input |
| `runtime_error` | The runtime reported an error |
| `idle_timeout` | Idle detection considers the task ready for input |
| `unknown` | Cockpit cannot identify a safe, specific waiting reason |

`turn_complete` does not mean the whole task is complete. With the CLI, send a normal follow-up only when `readyForNextPrompt` is `true`. Handle a visible permission or question first when the reason is `permission` or `question`.

## Follow up, queue, interrupt, and resume

- **Follow-up instruction**: starts the next turn of a task that is awaiting confirmation.
- **Queue**: holds an instruction sent while a task is running and starts it after the current turn ends.
- **Interrupt current turn**: stops the current processing and switches to the new instruction.
- **Resume**: reconnects an unfinished task whose process stopped, such as after an app restart, to its saved session.

`needsResume` is not a task state. It is additional information indicating that an unfinished task lost its process and requires a resume action.

In Cursor, Qoder, and Grok Build Native UI, resuming also restores the saved conversation from the connected session. A Grok Build workflow that was still in progress remains visible as in progress after the task resumes.

For Claude, Codex, Grok Build, Cursor, and Qoder, you can select an isolated account profile when creating a task. On a supported running task, switch from the account control beside the composer or with `cockpit task account <id> <name|id|default>`. Cockpit stops the current execution, moves the saved conversation, and resumes the same task under the selected profile.

## Conversation, quotes, and inter-task messages

Selecting text in the chat reveals **Quote selection**. Activating it inserts the selected content into the composer as a Markdown quote. Cockpit preserves the selection while task progress updates.

A message sent by another Cockpit task is labeled **Sent from another task** and shows the source task name or short ID. When the source task still exists in the current list, select its name to navigate to it.

A child task's status report to its parent appears as **Child task report**, separately from a regular message. The report identifies the child task and whether it is waiting for another instruction, permission, an answer, or review, or whether it encountered an error. When that child is still in the current task list, select **Go to task** to open it; expand **Technical details** to inspect the original machine-readable report.

In the PWA, a down-arrow button appears above the composer after you scroll away from the latest message. Select it to jump to the end of the conversation and resume following new output.

## Review and edit files

In the task-details right side panel, the pencil button can directly edit both files in Project Explorer and text files explicitly opened in File Preview. This includes files outside the project when they were opened by absolute path. Cockpit saves edits automatically after input and flushes unsaved content when you stop editing or move to another view.

Direct editing is limited to existing regular text files of 1 MB or less that the current user can write. Binary files, images, video, audio, PDFs, and files over 1 MB cannot be edited. Cockpit does not show a save confirmation or provide an in-app undo after writing, so verify the path at the top of the panel before selecting the pencil button. Changes to a file outside the project might not appear in the task's Git diff.

## Browser Identity and App Surface

Each task has one assigned Browser Identity. The in-app browser stores cookies, cache, localStorage, permissions, and related data in a persistent area owned by that Identity and does not copy the data to another Identity. A task without an explicit assignment uses the Default Identity, which preserves the existing browser profile and sign-ins.

The browser side panel shows the current Identity by name and color and lets you switch it. Switching changes the task assignment, and the next browser session uses the selected Identity's area. Existing sessions remain with their original Identity and become available again if you switch back. Create, rename, recolor, clear, or remove an Identity from **Browser Identity** in the lower-left app menu or from the CLI. The Default Identity cannot be removed.

App Surface connects a running Android emulator, Android physical device, or booted iOS Simulator to one task so a person and agent can inspect and operate its screen from a side panel. Cockpit does not boot the target or install, launch, or terminate its app, and one target cannot be attached to several tasks at the same time. The first connection to an Android physical device requires explicit approval through a Cockpit Ask. If the surface becomes **Detached** or **Stale**, select **Reconnect target** in the same panel to reconnect the previously selected target. Completing or deleting the task detaches the target without terminating its app.

## Attach files

In Desktop and the PWA, the new-task screen and task-details composer can attach images, text, source code, JSON, CSV, PDFs, audio, video, and office documents. When the selected agent supports a format natively, Cockpit sends it as a native attachment. Otherwise, Cockpit supplies its local path and metadata for the name, MIME type, and size. Handling varies by agent, UI mode, and model, so not every combination can interpret every format directly.

Attachments have these limits:

- Up to eight files per message, 512 MB per file, and 1 GB per message in total
- JSON files are limited to 25 MB each
- Archives and executable formats are not supported
- Cockpit validates the extension, MIME type, actual size, and content, then stores the upload under a randomized name rather than its original file name

An attachment's name and content are not automatically trusted instructions. State which file the agent should use and what result you expect in the message itself.

## History dashboard

The history dashboard in the header is available in guest mode. Alongside Cockpit activity, it can show local external sessions from Claude Code and Codex CLI and import them as tasks. Autorun and PWA remote access still require an AGI Labo membership.

## Complete and delete

Complete moves a task out of active work and into completed work. Delete removes the task record from Cockpit. They are different operations.

Completing a task in a temporary folder deletes that working directory automatically. Completing a Git-worktree task in Desktop silently preserves the worktree. By contrast, `cockpit task complete <id>` deletes the worktree by default and preserves it only with `--keep-worktree`. Before completing a task from the CLI, confirm where the required changes are stored and which option you need.

## Protect and recover task data

When saved tasks already exist, AGI Cockpit does not let an empty task list overwrite them unless you explicitly delete the final task.

If Cockpit cannot read `state.json` at startup, it stops before writing any task data and shows recovery guidance. Select **Open Data Folder**, restore `state.json` from the adjacent `backups` folder, and reopen AGI Cockpit. Cockpit does not continue startup by saving an empty task list over unreadable task data.

## Inspect state from the CLI

```bash
cockpit task list
cockpit task get <id>
cockpit task browser-identity <id>
```

`task get` returns `status`, `waitingReason`, `readyForNextPrompt`, and `needsResume`, together with the latest conversation and terminal output.

Use `cockpit task browser-identity <id> <name|id|default>` to change the Browser Identity assigned to a task. When creating a task, use `cockpit task create ... --browser-identity <name|id|default>` to assign one immediately.

## Related pages

- [Install and run your first task](https://agi-labo.com/en/tools/cockpit/docs/getting-started)
- [Ask](https://agi-labo.com/en/tools/cockpit/docs/ask)
- [Autorun](https://agi-labo.com/en/tools/cockpit/docs/autorun)
