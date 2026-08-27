<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Your first task

Choose a workspace and agent, safely run your first task, review its result, and mark the task complete.

> Verified with AGI Cockpit 4.61.0 on 2026-08-27. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/first-task)

This guide runs one short read-only request from task creation through result review and completion. If initial setup is not finished, start with [Install and complete initial setup](https://agi-labo.com/en/tools/cockpit/docs/getting-started).

## 1. Create a new task

1. Open **New task** at the top of the window.
2. Choose **Project workspace** or **Temporary folder**. In an existing project, select only the directory the agent may inspect.
3. Select an AI agent.
4. For supported agents, choose **Native UI** or **Terminal**.
5. If account selection is available, keep **Auto**. Keep the built-in system prompt.
6. Choose **Supervised** approval mode, enter the following request, and create the task.

```text
Inspect this folder and describe its main files and their roles in no more than five points. Do not change any files.
```

Project selection shows projects from the current task list first, followed by recently used projects. Search filters the display name and path. Enter a path directly or use the folder picker when a location is not listed.

A temporary folder is deleted automatically when the task is completed. In an existing project, begin only after you understand that the agent can operate on files in that directory.

## 2. Confirm that it is running

Cockpit adds the new task to the task list and selects it immediately. It normally enters **Running**, and task details show the instruction and the agent's progress or response. In Terminal UI, the terminal may appear first and connects to the same process when the runtime is ready.

If a sign-in notice appears, complete authentication for that agent. Native UI retries the same instruction. In Terminal UI or Terminal, sign in from the terminal and resume the task if needed.

## 3. Review the result

After one response finishes, the task enters **Awaiting confirmation**. This does not mean all work is complete; the task is ready for another instruction or decision.

Check whether the response satisfies the request. Send a follow-up from the task details composer if something is missing. For tasks that change files, review the diff and generated artifacts as well as the conversation.

## 4. Complete the task

When every required result is ready, select **Complete** in task details. Completed tasks move out of active work and do not accept ordinary follow-up instructions.

A temporary working directory is deleted on completion. Save any required result to a persistent location first.

## Create the first task from the CLI

After Cockpit integration is configured, create the same task from a supported AI agent or shell:

```bash
cockpit task create \
  --instruction "Inspect this folder in no more than five points. Do not change files." \
  --directory /path/to/project

cat instruction.md | cockpit task create --stdin --directory /path/to/project
cockpit task create --instruction-file instruction.md --directory /path/to/project
```

Use `--stdin` or `--instruction-file` for multiline content or text containing backticks, quotes, `$`, or code fences. If the directory is omitted, the task starts in an operating-system temporary folder.

## Read next

- Organize multiple pieces of work: [Task list](https://agi-labo.com/en/tools/cockpit/docs/tasks)
- Send follow-ups to selected work: [Task details](https://agi-labo.com/en/tools/cockpit/docs/task-details)
- Review diffs and files: [Results and tools](https://agi-labo.com/en/tools/cockpit/docs/results-and-tools)
