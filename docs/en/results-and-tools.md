<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Results and tools

Safely review task diffs, files, HTML Surfaces, Display notices, terminals, and logs, then continue to each dedicated operating surface.

> Verified with AGI Cockpit 4.88.0 on 2026-09-23. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/results-and-tools)

From the same task, you can inspect more than its conversation: changed code, files, reports, and running processes. This page covers shared result-review surfaces. Use [cockpit browser](https://agi-labo.com/en/tools/cockpit/docs/browser) for web operation and [App Surface](https://agi-labo.com/en/tools/cockpit/docs/app-surface) for a connected mobile screen.

## Use the right side panel

The right side panel in task details opens supporting information and interactive surfaces without leaving the conversation. It can show diffs, files, the attachment list, the project explorer, HTML Surfaces, the in-app browser, App Surfaces, side terminals, background logs, child tasks, and Talk Rooms when they are relevant.

Check the panel type and target path before acting. Closing the panel does not stop the task or its browser session. The panel remembers its width, and narrow layouts make long content and tables horizontally scrollable.

In the PWA, **Task panel** in task details combines HTML Surface, diff, Cockpit Browser, side terminal, Fleet, Creative Studio artifacts, and Talk Room as shared tabs. On a narrow screen it opens as a bottom sheet with previous and next controls, a horizontally scrolling tab row, and approximately half-height and full-height states. On a wide screen the panel sits beside the chat; a wider layout can also keep the task navigator visible. Changing width or orientation preserves the selected tab, target, drafts, and scroll positions for the task while the presentation switches. Closing the panel unsubscribes the view without terminating its terminal or browser. The attachment list, general files, the project explorer, App Surface, logs, and child tasks are not tabs in this PWA panel.

## Review diffs and files

The diff surface shows changes recognized by Git. Review additions, modifications, and deletions by file, and compare the actual scope with the agent's explanation before accepting completion. Untracked temporary files and files outside the project may not appear in the diff.

File preview supports text, images, audio, video, PDFs, and other recognized formats. Its toolbar stays in a separate row above the document and wraps its controls in a narrow panel, so it does not cover the content. A writable regular text file up to 1 MB can be edited with the pencil button. Changes save automatically, and Cockpit does not provide an undo action, so confirm the path and Git scope first.

When Desktop previews an `.html` or `.htm` file, **Open in the in-app browser** opens that local file in a new browser tab for the same task. If the file is being edited, Cockpit saves the current content first and does not open the browser when that save fails. This does not transfer an HTML Surface; it is a way to verify an HTML file from the working directory in a normal browser renderer.

The project explorer navigates the working directory hierarchy. It preserves the selected file across refreshes and opens files in preview. Inspect both content and path before sharing any file that could contain secrets.

## Find text in a Desktop side panel

Use **Find in content** or Cmd+F / Ctrl+F in the active file preview, editable text, diff, or HTML Surface. Enter and Shift+Enter move between matches; Esc closes the search. Search applies to that surface's text, not the whole project. Images, video, and text unavailable to the surface are not OCR-searched. Use the browser's own page search for an in-app browser tab.

## Distinguish HTML Mode from HTML Surface

HTML Mode is a skill an agent uses to compose an interactive HTML final result. HTML Surface is the product surface that stores that HTML for a task and displays it in the right side panel.

HTML Surface is not a general-purpose web browser. Use it for agent-created reports, comparisons, and dashboards; use the in-app browser for navigating external sites or operating forms. HTTP and HTTPS links in an HTML Surface open in the external browser, while other URL schemes are ignored.

From the CLI, `cockpit html show --stdin` stores HTML and `cockpit side-panel html` displays it. See the [`cockpit html` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/html) for the exact contract.

### Open an unseen HTML Surface

Desktop and the PWA count HTML Surfaces that have arrived but have not yet rendered in that task's visible HTML panel. A badge appears on the panel opener or HTML Surface tab. If you are already following the latest Surface, Cockpit follows the new one. If you are reviewing history or an older Surface, your view stays in place and a notice offers **Open latest**. Opening the unseen shortcut jumps to the newest unseen Surface; older unseen items remain marked in History.

A Surface becomes seen only after its content renders in the visible panel. The seen state persists across an app restart. Dismissing the arrival notice does not mark the Surface seen, and switching to another task does not clear its badge.

## Review Display notices

`cockpit display` puts a reminder, progress update, or lightweight status screen in front of the person as a Display notice that does not request a response. Desktop uses a dedicated window; the PWA includes notices with Asks in **Inbox**. When the originating task still exists, either surface can open that task directly.

Closing a Display notice sends no answer to the agent and resumes no task. When the same task repeats a notice with the same kind, title, and body, Cockpit reuses the open notice. A resend within ten seconds after closing is also treated as the same notice, so an agent does not retry merely because the duplicate was suppressed. Identical content from different tasks remains separate.

The CLI can list open notices and close one by ID:

```bash
cockpit display list
cockpit display list --task <task-id>
cockpit display close <display-id>
```

See the [`cockpit display` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/display) for the exact duplicate key, response fields, and errors.

## Continue to a dedicated operating surface

The in-app browser lets a person and agent operate the same real web page. See [cockpit browser](https://agi-labo.com/en/tools/cockpit/docs/browser) for page state, meaningful element targeting, and postconditions, and [Browser Identity](https://agi-labo.com/en/tools/cockpit/docs/browser-identities) for sign-in isolation.

App Surface attaches a running Android target or iOS Simulator to one task. See [App Surface](https://agi-labo.com/en/tools/cockpit/docs/app-surface) for readiness, attachment, accessibility actions, coordinates, recovery, and safety boundaries.

## Use side terminals and background logs

A side terminal is a shell session separate from the task's agent process. Before running a command, confirm the session name, current working directory, and target host. Closing a terminal ends that shell but does not complete the task.

In both PWA terminal views—the task's main Terminal view and a side terminal—the key bar sends Esc, Tab, Enter, and arrow keys directly to the host terminal. Open **Modifier keys** to send Ctrl+C, Ctrl+X, Ctrl+S, or Shift+Tab. These are terminal input sequences, not browser shortcuts, and have the same effect as typing them at the host: for example, Ctrl+C can interrupt the foreground process. The menu is disabled when the terminal is not writable.

Background logs show output from long-running processes started by an agent. In Antigravity Native UI, a command moved to the background remains visible after the turn completes with a Running, Completed, Failed, or Stopped state. Visible output alone is not completion. Check the exit code, generated artifact, listening port, test result, or another success condition appropriate to the request.

See the [`cockpit side-panel` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/side-panel) when controlling panels and terminals from the CLI.

## Use context-specific panels

The child tasks panel shows the state and reports of tasks that belong to a parent. See [Task management (CLI)](https://agi-labo.com/en/tools/cockpit/docs/task-management) for parent-child relationships and reports. The Talk Room panel shows a shared participant conversation. See [Talk Rooms](https://agi-labo.com/en/tools/cockpit/docs/talk-rooms) for participation, notifications, and mentions.

Skill details show the selected skill's instructions and usage conditions. A skill does not expand task permissions. Publishing, deletion, and external messaging remain subject to the normal approval boundary.

## Complete result review

Use this sequence to avoid treating a displayed result as a completed outcome:

1. Re-read the task objective and acceptance criteria.
2. Review the diff and any generated files outside Git.
3. Check applicable evidence such as tests, builds, screens, or network results.
4. Check for secrets, destructive changes, and external publication.
5. Save or share the required result before completing the task.

See [Task list](https://agi-labo.com/en/tools/cockpit/docs/tasks) for state, completion, and deletion, [Task details](https://agi-labo.com/en/tools/cockpit/docs/task-details) for follow-up and resume, and [Security and data](https://agi-labo.com/en/tools/cockpit/docs/security-and-data) for data boundaries.
