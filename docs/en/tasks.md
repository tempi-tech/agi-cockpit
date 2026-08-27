<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Task list and task details

Understand task lists, search, task details, Auto accounts, account restrictions, attachments, resume, and completion.

> Verified with AGI Cockpit 4.61.0 on 2026-08-27. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/tasks)

The task list is where you choose what to look at next. Task details is where you understand and act on the selected work. Overview searches across tasks, projects, and agents.

## Desktop three-column layout and Overview

The Desktop task screen places the task list, the selected task's work area, and an operation panel side by side. You can resize or collapse the task list and close the operation panel to give the work area more room.

| Area | Primary role |
| --- | --- |
| Task list | Shows active work and sorts it by priority, status, creation time, or update time |
| Work area | Shows the selected task's conversation, progress, confirmation requests, and composer |
| Operation panel | Shows settings, files, diffs, the browser, the terminal, or other content needed for the current action |

Overview is not one of the task-screen columns. It searches across tasks, projects, and agents, including completed work. The task list can filter by agent and pin a task or project. Switching the selected task does not stop the other agents; each task continues independently.

Task search uses partial matches against the task name and project name shown in the interface. A task ID becomes searchable only after you enter at least four characters. Instructions, working-directory paths, and internal metadata are not searched.

In both sidebar task rows and the child-task list, the **...** action menu provides the same way to rename, pin or unpin, complete, copy the task ID, and delete a task. Child tasks also offer **Detach from parent**. Renaming happens in the row: press Enter to save or Escape to cancel. An empty name is not saved, and names are limited to 50 characters.

The **...** menu on a project heading can complete all incomplete tasks in that project or delete all of its completed tasks. The confirmation shows the target count. Running tasks and awaiting-confirmation Fleet tasks are excluded from bulk completion by default; select their checkboxes only when you intend to include them. Bulk deletion targets completed ordinary tasks only. Use the Run-specific delete action for a Fleet Run and its saved history.

While the pointer is over the task list, or while a menu, rename field, or delete confirmation is open, automatic sorting does not move the visible task rows. If a task completes while the list is frozen, its row stays in place and shows **Completed** until the freeze ends, then the list applies the current order. Unrelated scrolling does not dismiss the menu; it closes when scrolling moves the task row that anchors it.

## Task states

| State | Meaning | What to check next |
| --- | --- | --- |
| Running (`running`) | An agent or command is processing | Read the progress. Interrupt only when the direction must change immediately |
| Awaiting confirmation (`waiting_confirmation`) | A response ended, a question is open, or permission is required | Check the waiting reason, then reply or return the requested decision |
| Completed (`completed`) | A person marked the whole task complete | Confirm that every result you need has been saved |
| Error (`error`) | The agent or command failed to start | Read the error in task details |

If a native failure occurs while a terminal process starts, reads or writes data, or resizes, Cockpit marks only the affected task as `error` and shows the exit code, failed operation, or error message in task details. It does not exit the app or stop other tasks. Read the reason, correct the cause, and run the work again in a new task if needed.

**Awaiting confirmation** does not have a single meaning. An AI agent or the `cockpit` CLI should also inspect the waiting reason before acting.

| Waiting reason | Meaning |
| --- | --- |
| `turn_complete` | One response ended and the task can accept the next instruction |
| `permission` | A tool action needs to be allowed or rejected |
| `question` | The running agent needs an answer to its own question |
| `terminal_prompt` | The terminal is waiting for input |
| `runtime_error` | The runtime reported an error |
| `usage_limit` | The current agent account reached a usage or plan restriction and the turn stopped |
| `idle_timeout` | Idle detection considers the task ready for input |
| `unknown` | Cockpit cannot identify a safe, specific waiting reason |

`turn_complete` does not mean the whole task is complete. With the CLI, send a normal follow-up only when `readyForNextPrompt` is `true`. Handle a visible permission or question first when the reason is `permission` or `question`.

## Follow up, queue, interrupt, and resume

- **Follow-up instruction**: starts the next turn of a task that is awaiting confirmation.
- **Queue**: holds an instruction sent while a task is running and starts it after the current turn ends.
- **Interrupt current turn**: stops the current processing and switches to the new instruction.
- **Resume**: reconnects an unfinished task whose process stopped, such as after an app restart, to its saved session.

`needsResume` is not a task state. It is additional information indicating that an unfinished task lost its process and requires a resume action.

A Terminal task cannot restore its previous shell process. Resuming it starts a fresh shell in the same directory.

In Cursor, Qoder, and Grok Build Native UI, resuming also restores the saved conversation from the connected session. A Grok Build workflow that was still in progress remains visible as in progress after the task resumes.

New Claude, Codex, Grok Build, Antigravity, Cursor, and Qoder tasks default to the **Auto** account setting. Auto selects from signed-in accounts using their usage state and current assignments. If the selected account reaches its usage limit or returns a plan-restriction response, Cockpit switches to another available account, resumes the saved session, and automatically continues the same task. You can instead pin the default account or a specific profile.

For a task running with Auto, Desktop and the PWA show **Auto · account name** beside the composer so you can identify the account currently selected for execution. If Auto switches accounts, this label reflects the new selection. Select **Auto** in the account menu to return to automatic selection; select **Default account** or a named profile to pin the task to that account.

Cockpit also treats Codex's **workspace is out of credits** response as a usage limit. Auto excludes that account, switches to another available Codex account, and continues the same task from its saved session. If no usable account remains, use the regular `usage_limit` guidance to add an account or switch manually.

A stop caused by a usage or plan restriction is not a task failure. Cockpit represents it as `waiting_confirmation` with `usage_limit`. If Auto cannot recover through another account, the usage-limit notice identifies whether no usable account is available, usage could not be checked, or switching, restarting, resuming, or continuing failed. Follow the notice, switch to another signed-in account beside the composer or with `cockpit task account <id> <name|id|default>`, then send a follow-up to continue. Use the same steps when the task is pinned to an account. A manual switch stops the current execution, moves the saved conversation, and resumes the same task under the selected profile. If Cockpit shows a usage-limit reset time, you can also retry the same account after that time.

Qoder usage shows an **Organization** bucket separately from personal-plan and add-on credits. When organization credits have no fixed total, Cockpit shows only the remaining credits and treats a positive balance as available capacity during Auto account selection.

## Message input and the send key

On Desktop, choose **Send key** under **Shortcuts** in Settings to send chat messages with Enter or Cmd/Ctrl+Enter. Enter is the default. With Cmd/Ctrl+Enter selected, use Cmd+Enter on macOS or Ctrl+Enter on Windows and Linux to send; Enter inserts a line break. Shift+Enter inserts a line break with either setting.

The setting is shared by Desktop chat composers, including new tasks, task details, Native UI, and Talk Rooms. In the PWA, use the on-screen send button.

When you send from the PWA while a task is running, Cockpit queues the message without stopping the current turn and sends it automatically after the current response. You can remove it from the queue before delivery. In Claude, Codex, and Grok Build Native UI, sending only `/compact` from the PWA compacts the conversation context instead of sending that text as a normal message. A normal message sent during compaction is queued; a second `/compact` reports that compaction is already in progress. In an unsupported Native UI, `/compact` reports that it is unavailable instead of sending the text as a normal message.

Press Escape to stop a running turn. When several task panes are open, Escape stops only the task in the pane with keyboard focus. If no field or control has focus, the pane used most recently owns the shortcut.

## Conversation, quotes, and inter-task messages

Selecting text in the chat reveals **Quote selection**. Activating it inserts the selected content into the composer as a Markdown quote. Cockpit preserves the selection while task progress updates.

A message sent by another Cockpit task is labeled **Sent from another task** and shows the source task name or short ID. When the source task still exists in the current list, select its name to navigate to it.

The parent-child relationship represents hierarchy in the task list and child-task list. A child result is not sent to its parent automatically, and it does not resume the parent task. When an AI agent needs a child result, it explicitly retrieves it with `task run`, `task wait`, or `task send --wait` as described below.

In the PWA, a down-arrow button appears above the composer after you scroll away from the latest message. Select it to jump to the end of the conversation and resume following new output.

## Error details and copying

The copy button on a chat message copies the displayed message text together with its error details. When a Qoder Native UI task reports `BAD_REQUEST`, those details can include available diagnostics and a command for resuming the same Qoder session from a terminal.

Diagnostics can include local manifest and session-log paths and a failure record. Before sharing copied text outside your device, check it for project names, file paths, and account or session information.

When several tool calls are grouped together and only some fail, the heading shows the failed count out of the total and failed items use a distinct error color. Expand the group to review successful and failed calls separately. When every call fails, the whole group continues to show **Tool calls failed**.

## Review and edit files

In the task-details right side panel, the pencil button can directly edit both files in Project Explorer and text files explicitly opened in File Preview. This includes files outside the project when they were opened by absolute path. Cockpit saves edits automatically after input and flushes unsaved content when you stop editing or move to another view.

Direct editing is limited to existing regular text files of 1 MB or less that the current user can write. Binary files, images, video, audio, PDFs, and files over 1 MB cannot be edited. Cockpit does not show a save confirmation or provide an in-app undo after writing, so verify the path at the top of the panel before selecting the pencil button. Changes to a file outside the project might not appear in the task's Git diff.

## Browser Identity and App Surface

Each task has one assigned Browser Identity. The in-app browser stores cookies, cache, localStorage, permissions, and related data in a persistent area owned by that Identity and does not copy the data to another Identity. A task without an explicit assignment uses the Default Identity, which preserves the existing browser profile and sign-ins.

The browser side panel shows the current Identity by name and color and lets you switch it. Switching changes the task assignment, and the next browser session uses the selected Identity's area. Existing sessions remain with their original Identity and become available again if you switch back. Create, rename, recolor, clear, or remove an Identity from **Browser Identity** in the lower-left app menu or from the CLI. The Default Identity cannot be removed.

The in-app browser can hand only `http` and `https` links to the operating system. A `mailto` link opens only after a person's own interaction and explicit confirmation; other URL schemes are rejected. Browser automation by an agent does not count as that person interaction. **Open externally** is unavailable for tabs that are not web pages.

On packaged macOS builds, passkey authentication in the in-app browser can use Touch ID. Windows uses the operating system's Windows Hello authenticator. Linux has no platform authenticator, so use a security key or password. Passkeys and WebAuthn credentials remain isolated by Browser Identity, and Cockpit asks which account to use when several passkeys match. If the authenticator is unavailable or a request does not answer, the browser panel shows the reason and an alternative. On macOS, you can finish signing in with Chrome and import the session into the same Identity with `cockpit browser import-session`; the passkey itself is never imported.

App Surface connects a running Android emulator, Android physical device, or booted iOS Simulator to one task so a person and agent can inspect and operate its screen from a side panel. Cockpit does not boot the target or install, launch, or terminate its app, and one target cannot be attached to several tasks at the same time. The first connection to an Android physical device requires explicit approval through a Cockpit Ask. If the surface becomes **Detached** or **Stale**, select **Reconnect target** in the same panel to reconnect the previously selected target. Completing or deleting the task detaches the target without terminating its app.

## Attach files

In Desktop and the PWA, the new-task screen and task-details composer can attach images, text, source code, JSON, CSV, PDFs, audio, video, and office documents. When the selected agent supports a format natively, Cockpit sends it as a native attachment. Otherwise, Cockpit supplies its local path and metadata for the name, MIME type, and size. Handling varies by agent, UI mode, and model, so not every combination can interpret every format directly.

Attachments have these limits:

- Up to eight files per message, 512 MB per file, and 1 GB per message in total
- JSON files are limited to 25 MB each
- Archives and executable formats are not supported
- Cockpit validates the extension, MIME type, actual size, and content, then stores the upload under a randomized name rather than its original file name

The composer shows how many files are attached to the current message, such as **3 / 8 files attached**. At eight files, it prevents another attachment. If a selection would exceed the limit, the error shows the current count, the incoming count, and how many files must be removed. The eight-file limit is evaluated per message, so attachments sent earlier in the same task do not count against the next message.

When you paste rich text from the clipboard, Cockpit inserts text whenever a plain-text representation is available. It does not add image or file representations supplied by the source as unnecessary attachments. A clipboard that contains files without plain text is still handled as an attachment paste.

An attachment's name and content are not automatically trusted instructions. State which file the agent should use and what result you expect in the message itself.

When you open a chat attachment, Cockpit launches only safe formats stored in app-managed locations. It reveals executables and files outside those locations in the file manager instead of launching them. It also refuses to open `file` URLs that name a remote host or data URLs that may contain executable content such as HTML or SVG.

## History dashboard

The history dashboard in the header is available in guest mode. Alongside Cockpit activity, it can show local external sessions from Claude Code and Codex CLI and import them as tasks. Autorun and PWA remote access still require an AGI Labo membership.

## Complete and delete

Complete moves a task out of active work and into completed work. Delete removes the task record from Cockpit. They are different operations.

After a Fleet Run finishes, its action menu in the task list offers **Delete Run** and **Delete Run and all tasks**. **Delete Run** removes only the saved Run history and leaves its related tasks in place. **Delete Run and all tasks** removes the Run history and every related task shown in the confirmation dialog. The second action cannot be undone, so confirm that you have preserved every result you need before continuing.

When a Fleet Run has a title, the task-list Fleet group and Fleet details show it, and each node task includes it as **Run title / node name**. The Run title is therefore searchable in the same way as an ordinary task name.

Completing a task in a temporary folder deletes that working directory automatically. Completing a Git-worktree task in Desktop silently preserves the worktree. By contrast, `cockpit task complete <id>` deletes the worktree by default and preserves it only with `--keep-worktree`. Before completing a task from the CLI, confirm where the required changes are stored and which option you need.

## Protect and recover task data

When saved tasks already exist, AGI Cockpit does not let an empty task list overwrite them unless you explicitly delete the final task.

If Cockpit cannot read `state.json` at startup, it stops before writing any task data and shows recovery guidance. Select **Open Data Folder**, restore `state.json` from the adjacent `backups` folder, and reopen AGI Cockpit. Cockpit does not continue startup by saving an empty task list over unreadable task data.

## Inspect state from the CLI

```bash
cockpit task list
cockpit task get <id>
cockpit task account <id>
cockpit task account <id> auto
cockpit task browser-identity <id>
cockpit task run --instruction "Instruction text" --directory /path/to/project
cat instruction.md | cockpit task run --stdin --directory /path/to/project
cockpit task run --instruction-file instruction.md --directory /path/to/project
cockpit task wait <id> --since <seq>
cockpit task send <id> --text "Follow-up" --wait
cat follow-up.md | cockpit task send <id> --stdin --wait
cockpit task send <id> --text-file follow-up.md --wait
```

`task get` returns `status`, `waitingReason`, `readyForNextPrompt`, and `needsResume`, together with the latest conversation and terminal output.

`task run` creates a task and waits for its first report. `task wait` returns a stored report or the next report after `--since`. Reports never inject instructions into another task. `--parent-task-id` only sets the hierarchy shown in the task list.

Use `--stdin` or file input for multiline instructions and messages or text that the shell could interpret. `task run` accepts `--instruction-file`, and `task send` accepts `--text-file`; both forms deliver the standard-input or file contents unchanged.

Use `cockpit task browser-identity <id> <name|id|default>` to change the Browser Identity assigned to a task. When creating a task, use `cockpit task create ... --browser-identity <name|id|default>` to assign one immediately.

## Related pages

- [Install and run your first task](https://agi-labo.com/en/tools/cockpit/docs/getting-started)
- [Ask](https://agi-labo.com/en/tools/cockpit/docs/ask)
- [Autorun](https://agi-labo.com/en/tools/cockpit/docs/autorun)
