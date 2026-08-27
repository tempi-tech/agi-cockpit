<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Results and tools

Safely review task diffs, files, HTML Surfaces, terminals, and logs, then continue to each dedicated operating surface.

> Verified with AGI Cockpit 4.61.0 on 2026-08-27. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/results-and-tools)

From the same task, you can inspect more than its conversation: changed code, files, reports, and running processes. This page covers shared result-review surfaces. Use [cockpit browser](https://agi-labo.com/en/tools/cockpit/docs/browser) for web operation and [App Surface](https://agi-labo.com/en/tools/cockpit/docs/app-surface) for a connected mobile screen.

## Use the right side panel

The right side panel in task details opens supporting information and interactive surfaces without leaving the conversation. It can show diffs, files, the project explorer, HTML Surfaces, the in-app browser, App Surfaces, side terminals, background logs, child tasks, and Talk Rooms when they are relevant.

Check the panel type and target path before acting. Closing the panel does not stop the task or its browser session. The panel remembers its width, and narrow layouts make long content and tables horizontally scrollable.

## Review diffs and files

The diff surface shows changes recognized by Git. Review additions, modifications, and deletions by file, and compare the actual scope with the agent's explanation before accepting completion. Untracked temporary files and files outside the project may not appear in the diff.

File preview supports text, images, audio, video, PDFs, and other recognized formats. A writable regular text file up to 1 MB can be edited with the pencil button. Changes save automatically, and Cockpit does not provide an undo action, so confirm the path and Git scope first.

The project explorer navigates the working directory hierarchy. It preserves the selected file across refreshes and opens files in preview. Inspect both content and path before sharing any file that could contain secrets.

## Distinguish HTML Mode from HTML Surface

HTML Mode is a skill an agent uses to compose an interactive HTML final result. HTML Surface is the product surface that stores that HTML for a task and displays it in the right side panel.

HTML Surface is not a general-purpose web browser. Use it for agent-created reports, comparisons, and dashboards; use the in-app browser for navigating external sites or operating forms. HTTP and HTTPS links in an HTML Surface open in the external browser, while other URL schemes are ignored.

From the CLI, `cockpit html show --stdin` stores HTML and `cockpit side-panel html` displays it. See the [`cockpit html` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/html) for the exact contract.

## Continue to a dedicated operating surface

The in-app browser lets a person and agent operate the same real web page. See [cockpit browser](https://agi-labo.com/en/tools/cockpit/docs/browser) for page state, meaningful element targeting, and postconditions, and [Browser Identity](https://agi-labo.com/en/tools/cockpit/docs/browser-identities) for sign-in isolation.

App Surface attaches a running Android target or iOS Simulator to one task. See [App Surface](https://agi-labo.com/en/tools/cockpit/docs/app-surface) for readiness, attachment, accessibility actions, coordinates, recovery, and safety boundaries.

## Use side terminals and background logs

A side terminal is a shell session separate from the task's agent process. Before running a command, confirm the session name, current working directory, and target host. Closing a terminal ends that shell but does not complete the task.

Background logs show output from long-running processes started by an agent. Visible output alone is not completion. Check the exit code, generated artifact, listening port, test result, or another success condition appropriate to the request.

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
