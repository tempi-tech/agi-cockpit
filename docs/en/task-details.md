<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Task details

Operate the selected task's conversation, follow-ups, queue, interruption, resume, account, attachments, and errors.

> Verified with AGI Cockpit 4.70.0 on 2026-09-05. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/task-details)

Task details is where you understand a piece of work selected from the [Task list](https://agi-labo.com/en/tools/cockpit/docs/tasks) and return the next instruction or decision. It combines the conversation, progress, confirmation requests, composer, and the task's right-side panels.

## Follow up, queue, interrupt, and resume

- **Follow-up** sends the next turn to a task awaiting confirmation.
- **Queue** holds instructions sent while the current turn is running and delivers them in order afterward.
- **Interrupt current turn** stops current processing and switches to the instruction you entered.
- **Resume** reconnects an unfinished task to its saved session after its process stopped, such as after an app restart.

A Terminal task cannot restore its previous shell process, so resume starts a new shell in the same directory. Native UI for Cursor, Qoder, and Grok Build reconnects to its supported saved conversation.

After three consecutive identical turn failures in Visual Runtime, Cockpit marks the conversation **Session cannot be resumed** and stops retrying that rejected session. Inspect the last error, then run `cockpit task resume <id> --fresh-session` to start a new conversation in the same task with a summary of the stored conversation. Do not use an ordinary resume to return to the rejected session.

Escape stops a running turn. With multiple task panes, it applies only to the pane with keyboard focus.

Stopping a turn, completing or removing a task, or quitting the app also stops descendant processes launched by that task. A process explicitly launched outside the task lifecycle is outside Cockpit's cleanup boundary, so detach one only when it is intended to remain running.

The **Follow-up behavior** setting under Appearance chooses whether an ordinary follow-up sent during a reply waits in the **Queue** or **Steers** the current reply. Queue is the default. When it does not conflict with the configured send or steer shortcut, Cmd/Ctrl+Enter uses the opposite behavior for that one message.

Before delivery, a queued message can be edited, sent now, or removed. **Edit** returns its text and attachments to the composer and removes that entry from the queue; revise it and send it again when ready.

## Confirm the execution account

New Claude, Codex, Grok Build, Antigravity, Cursor, and Qoder tasks default to **Auto**. Auto uses shorter usage windows to confirm current availability, then chooses among available accounts from the remaining capacity and reset time of the longest window. After detecting a usage or plan limit, it can switch to another available account and continue the saved session.

The selection made when the task is created is saved with that task and remains the selection used for display, resume, and execution. Desktop and PWA show **Auto · account name** near the composer. The account menu can select Auto, the default account, or a named profile. A manual switch stops current execution, moves the saved conversation to the chosen profile, and resumes it.

If no account is available, usage cannot be checked, or switching or resume fails, the task stops with `waiting_confirmation` and `usage_limit`. When a reset time is known, Cockpit re-evaluates usage after that time and resumes the task if capacity has recovered. If the account remains exhausted, usage cannot be confirmed, or authentication has expired, the task shows the reason and recovery action. Switch accounts or sign in again before retrying.

## Enter a message

Desktop's **Send key** setting assigns sending to Enter or Cmd/Ctrl+Enter. Shift+Enter inserts a newline in either mode. On supported Desktop systems, the microphone records up to 90 seconds and transcribes locally. Review the inserted text before sending.

Each task keeps its own draft text and attachments. If you switch tasks before a send or interrupt result arrives, failed input returns to the originating task's composer, and a delayed Visual Runtime error appears on that task. Restored text is prepended to any newer draft entered for the same task in the meantime.

PWA uses the same Follow-up behavior setting and queue as Desktop. In Claude, Codex, and Grok Build Native UI, sending only `/compact` from PWA compacts the conversation context.

## Read and quote the conversation

Select chat text and choose **Quote selection** to add it to the composer as a Markdown quote. The selection is preserved while progress updates.

A message from another Cockpit task displays its source task name or shortened ID. If the source remains in the list, select the name to navigate there. A parent-child relationship alone does not send results or automatically resume the parent.

On PWA, a down-arrow button appears after you scroll away from the end. Select it to return to the newest message and follow new output.

Saved Native UI history is restored by conversation turn. When older history has been compacted into archives, the notice counts omitted turns rather than individual events. A tool item whose complete output cannot be restored from saved history displays **Some output omitted** in both Desktop and the PWA.

When a task detail is open in the PWA, its URL uses `#task/<task-id>` as a deep link. On initial launch or during reconnection, Cockpit waits for the task list to synchronize before opening that task, and browser Back returns to the list. A deleted or unknown ID returns to the list and shows **Task not found**.

Selecting an OS notification for a completed response, confirmation request, usage limit, or error brings Desktop to the foreground and opens the source task. Selecting the notification does not answer an Ask, approve a tool, or complete the task.

## Inspect errors and tool runs

When Claude, Codex, Antigravity, Cursor, or Grok Build reports an error that may indicate a service incident, such as a 404, 5xx response, or gateway timeout, the error surface links directly to that provider's status page. Cockpit does not show this link for authentication, usage-limit, quota, rate-limit, or billing errors; follow the on-screen sign-in, account-switching, or wait guidance instead.

A message's copy button includes the visible body and error details. Diagnostics may contain local file paths, session logs, or account information, so inspect the copied content before sharing it externally.

When several tool runs are grouped and only some fail, the heading shows the failed count. Do not treat the successful entries as proof that the whole group succeeded; inspect each failed operation and its effect.

## Review and edit files

A task's file action opens the target in the right side panel. A writable regular text file up to 1 MB can be edited with the pencil button, and changes save automatically. Cockpit provides no undo action, so confirm the path and Git scope first.

See [Results and tools](https://agi-labo.com/en/tools/cockpit/docs/results-and-tools) for diffs, HTML Surfaces, the browser, App Surface, and terminals. Use [cockpit browser](https://agi-labo.com/en/tools/cockpit/docs/browser) for web operation and [App Surface](https://agi-labo.com/en/tools/cockpit/docs/app-surface) for connected mobile screens.

## Attach files

Desktop and PWA can attach images, text, source code, JSON, CSV, PDFs, audio, video, and Office documents. When a selected agent cannot receive the format directly, Cockpit provides its local path and metadata for name, MIME type, and size.

For an image sent to Antigravity Native UI, Cockpit uses an image already inside the workspace in place. It temporarily copies an external image into a per-session, Git-ignored directory under `.agi-cockpit-attachments` in the workspace. This makes it readable in `supervised` mode. Cockpit removes the temporary copy when the session, CLI, or app stops, and sweeps old leftover directories when another session starts in that workspace. If the workspace is not writable or the staging location is not a real directory, Cockpit reports an error without sending the image.

One message accepts up to eight files, each up to 512 MB and 1 GB in total. JSON files are limited to 25 MB, and archives and executable formats are unsupported. Cockpit validates extension, MIME type, actual size, and content, then stores the upload under a randomized name.

The limit applies per message, so earlier attachments do not count toward the next eight. When clipboard content has a plain-text representation, Cockpit inserts the text instead of attaching unnecessary image representations.

An attachment's name and content are not automatically trusted instructions. State which file to use, why, and what result you expect in the message. Cockpit opens only safe formats inside app-managed storage; it does not directly launch executables, paths outside that storage, remote `file` URLs, or data URLs that may contain executable HTML or SVG.

## Continue from the CLI

```bash
cockpit task get <id>
cockpit task account <id> auto
cockpit task resume <id> --fresh-session
cockpit task send <id> --text "Follow-up" --wait
cat follow-up.md | cockpit task send <id> --stdin --wait
cockpit task send <id> --text-file follow-up.md --wait
cockpit task wait <id> --since <seq>
```

Use `--stdin` or `--text-file` for multiline or shell-sensitive content. `task wait` returns a saved report or the next report after the sequence; it never injects instructions into another task.

## Related pages

- [Task list](https://agi-labo.com/en/tools/cockpit/docs/tasks)
- [Ask](https://agi-labo.com/en/tools/cockpit/docs/ask)
- [Browser Identity](https://agi-labo.com/en/tools/cockpit/docs/browser-identities)
- [Results and tools](https://agi-labo.com/en/tools/cockpit/docs/results-and-tools)
