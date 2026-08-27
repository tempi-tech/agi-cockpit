<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Results and tools

Learn how to safely review and operate task diffs, files, HTML Surfaces, the browser, App Surfaces, terminals, and logs.

> Verified with AGI Cockpit 4.61.0 on 2026-08-27. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/results-and-tools)

From the same task, you can inspect more than its conversation: changed code, files, web pages, connected app screens, and running processes. This page distinguishes the surfaces used to review results from the tools that can take action.

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

## Operate the in-app browser

The in-app browser opens real web pages using a task session and a Browser Identity. Cookies, cache, localStorage, permissions, and proxy authentication are isolated by Identity. In-memory tab state and saved sessions remain available when you switch tasks or hide the panel.

When an agent clicks, types, selects, uploads, pastes, presses a key, or scrolls, the CLI reports event delivery and observable changes. `changed: false` is not proof of failure or success. Verify the intended postcondition through the URL, visible text, element state, or network result. Read the current page before retrying after a failure because a second click can submit twice.

Passkeys can use Touch ID in a signed macOS build and Windows Hello on Windows. Linux has no platform authenticator integration, so use a security key or password. See the [`cockpit browser` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/browser).

## Work with app screens through App Surface

App Surface attaches an already-running Android emulator, Android physical device, or booted iOS Simulator to one task. Cockpit does not boot, start, stop, or install the target or its app, and one target cannot be attached to multiple tasks at the same time.

Run `cockpit app doctor` and `cockpit app targets` before attaching. After attachment, prefer accessibility snapshots and target by label, role, a reference from the latest snapshot, then coordinates. Input delivery does not prove that the app reacted, so verify with a postcondition such as `--expect-text` or a fresh snapshot. The first attachment to an Android physical device requires approval through Ask.

An offline or stale Surface keeps its last frame but rejects interaction. Reattach and confirm health. See the [`cockpit app` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/app) for setup, error codes, and coordinate rules.

## Use side terminals and background logs

A side terminal is a shell session separate from the task's agent process. Before running a command, confirm the session name, current working directory, and target host. Closing a terminal ends that shell but does not complete the task.

Background logs show output from long-running processes started by an agent. Visible output alone is not completion. Check the exit code, generated artifact, listening port, test result, or another success condition appropriate to the request.

See the [`cockpit side-panel` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/side-panel) when controlling panels and terminals from the CLI.

## Use context-specific panels

The child tasks panel shows the state and reports of tasks that belong to a parent. The Talk Room panel shows a shared participant conversation. Neither surface automatically delivers every result to a parent task. See [Orchestration](https://agi-labo.com/en/tools/cockpit/docs/orchestration) for parent-child tasks, reports, and Talk Rooms.

Skill details show the selected skill's instructions and usage conditions. A skill does not expand task permissions. Publishing, deletion, and external messaging remain subject to the normal approval boundary.

## Complete result review

Use this sequence to avoid treating a displayed result as a completed outcome:

1. Re-read the task objective and acceptance criteria.
2. Review the diff and any generated files outside Git.
3. Check applicable evidence such as tests, builds, screens, or network results.
4. Check for secrets, destructive changes, and external publication.
5. Save or share the required result before completing the task.

See [Tasks and task details](https://agi-labo.com/en/tools/cockpit/docs/tasks) for task state, resume, completion, and deletion, and [Security and data](https://agi-labo.com/en/tools/cockpit/docs/security-and-data) for data boundaries.
