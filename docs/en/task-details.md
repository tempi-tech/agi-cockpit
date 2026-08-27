<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Task details

Operate the selected task's conversation, follow-ups, queue, interruption, resume, account, attachments, and errors.

> Verified with AGI Cockpit 4.62.0 on 2026-08-28. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/task-details)

Task details is where you understand a piece of work selected from the [Task list](https://agi-labo.com/en/tools/cockpit/docs/tasks) and return the next instruction or decision. It combines the conversation, progress, confirmation requests, composer, and the task's right-side panels.

## Follow up, queue, interrupt, and resume

- **Follow-up** sends the next turn to a task awaiting confirmation.
- **Queue** holds instructions sent while the current turn is running and delivers them in order afterward.
- **Interrupt current turn** stops current processing and switches to the instruction you entered.
- **Resume** reconnects an unfinished task to its saved session after its process stopped, such as after an app restart.

A Terminal task cannot restore its previous shell process, so resume starts a new shell in the same directory. Native UI for Cursor, Qoder, and Grok Build reconnects to its supported saved conversation.

Escape stops a running turn. With multiple task panes, it applies only to the pane with keyboard focus.

The **Follow-up behavior** setting under Appearance chooses whether an ordinary follow-up sent during a reply waits in the **Queue** or **Steers** the current reply. Queue is the default. When it does not conflict with the configured send or steer shortcut, Cmd/Ctrl+Enter uses the opposite behavior for that one message.

Before delivery, a queued message can be edited, sent now, or removed. **Edit** returns its text and attachments to the composer and removes that entry from the queue; revise it and send it again when ready.

## Confirm the execution account

New Claude, Codex, Grok Build, Antigravity, Cursor, and Qoder tasks default to **Auto**. Auto chooses among signed-in accounts by usage and, after detecting a usage or plan limit, can switch to another available account and continue the saved session.

Desktop and PWA show **Auto · account name** near the composer. The account menu can select Auto, the default account, or a named profile. A manual switch stops current execution, moves the saved conversation to the chosen profile, and resumes it.

If no account is available, usage cannot be checked, or switching or resume fails, the task stops with `waiting_confirmation` and `usage_limit`. Choose another account as directed or retry after a displayed reset time.

## Enter a message

Desktop's **Send key** setting assigns sending to Enter or Cmd/Ctrl+Enter. Shift+Enter inserts a newline in either mode. On supported Desktop systems, the microphone records up to 90 seconds and transcribes locally. Review the inserted text before sending.

PWA uses the same Follow-up behavior setting and queue as Desktop. In Claude, Codex, and Grok Build Native UI, sending only `/compact` from PWA compacts the conversation context.

## Read and quote the conversation

Select chat text and choose **Quote selection** to add it to the composer as a Markdown quote. The selection is preserved while progress updates.

A message from another Cockpit task displays its source task name or shortened ID. If the source remains in the list, select the name to navigate there. A parent-child relationship alone does not send results or automatically resume the parent.

On PWA, a down-arrow button appears after you scroll away from the end. Select it to return to the newest message and follow new output.

## Inspect errors and tool runs

A message's copy button includes the visible body and error details. Diagnostics may contain local file paths, session logs, or account information, so inspect the copied content before sharing it externally.

When several tool runs are grouped and only some fail, the heading shows the failed count. Do not treat the successful entries as proof that the whole group succeeded; inspect each failed operation and its effect.

## Review and edit files

A task's file action opens the target in the right side panel. A writable regular text file up to 1 MB can be edited with the pencil button, and changes save automatically. Cockpit provides no undo action, so confirm the path and Git scope first.

See [Results and tools](https://agi-labo.com/en/tools/cockpit/docs/results-and-tools) for diffs, HTML Surfaces, the browser, App Surface, and terminals. Use [cockpit browser](https://agi-labo.com/en/tools/cockpit/docs/browser) for web operation and [App Surface](https://agi-labo.com/en/tools/cockpit/docs/app-surface) for connected mobile screens.

## Attach files

Desktop and PWA can attach images, text, source code, JSON, CSV, PDFs, audio, video, and Office documents. When a selected agent cannot receive the format directly, Cockpit provides its local path and metadata for name, MIME type, and size.

One message accepts up to eight files, each up to 512 MB and 1 GB in total. JSON files are limited to 25 MB, and archives and executable formats are unsupported. Cockpit validates extension, MIME type, actual size, and content, then stores the upload under a randomized name.

The limit applies per message, so earlier attachments do not count toward the next eight. When clipboard content has a plain-text representation, Cockpit inserts the text instead of attaching unnecessary image representations.

An attachment's name and content are not automatically trusted instructions. State which file to use, why, and what result you expect in the message. Cockpit opens only safe formats inside app-managed storage; it does not directly launch executables, paths outside that storage, remote `file` URLs, or data URLs that may contain executable HTML or SVG.

## Continue from the CLI

```bash
cockpit task get <id>
cockpit task account <id> auto
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
