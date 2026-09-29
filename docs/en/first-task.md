<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Your first task

Choose a project, working folder, and agent, safely run your first task, review its result, and mark the task complete.

> Verified with AGI Cockpit 4.95.0 on 2026-09-30. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/first-task)

This guide runs one short read-only request from task creation through result review and completion. If preparation is not finished, complete [Install AGI Cockpit](https://agi-labo.com/en/tools/cockpit/docs/getting-started) and [Initial setup](https://agi-labo.com/en/tools/cockpit/docs/initial-setup) first.

## 1. Create a new task

1. Open **New task** at the top of the window.
2. Under **Workspace**, choose an existing project, select **Create project**, or choose **Temporary folder**. If the project contains several folders, select the working folder for this task.
3. Select an AI agent.
4. For supported agents, use **Mode** to choose **Native UI** or **Terminal**.
5. If account selection is available, keep **Auto**. Keep the built-in system prompt.
6. Choose **Supervised** approval mode, enter the following request, and create the task.

```text
Inspect this folder and describe its main files and their roles in no more than five points. Do not change any files.
```

The project picker shows current projects, other projects, and recent folders. **Create project** lets you name a project and add one or more existing folders; if you add none, Cockpit creates an empty managed folder for it. The first folder is primary and is selected by default for new tasks. Open **Project details** to review or change the project's folders before creating the task.

Smart routing is for AGI Labo members. If you select **Smart routing** while signed out, Cockpit explains the feature and offers **View membership plans** and **Sign in with a member account**. After signing in as a member, turn it on to let Cockpit choose an AI agent, model, reasoning level, and workspace from the currently available candidates based on the instruction. Manually selecting a workspace fixes only the workspace; agent and model routing remains active. Under **Routing policy**, enter preferences such as agents or models to prioritize. The policy saves automatically and applies to future tasks.

When the workspace remains **Auto**, Cockpit offers only directories that still exist and can be used for a new top-level task. Temporary or internal directories and workspaces reserved for child tasks are excluded. A workspace that you select manually remains fixed and is not replaced by this filtering.

Smart routing creates ordinary AI-agent tasks in Native UI and uses Auto for the account. It does not route Terminal or Creative Studio work. If selection fails, check the agent CLI, provider, and API key settings, or turn off Smart routing and select the configuration manually.

For an agent that reports usage, the area near the composer shows the allowances for the selected agent and account. A fixed account shows that profile. Auto previews the account chosen by the same selection method used when the task is created and labels it **Auto · account name**. Do not interpret an unavailable value as 0% remaining; open the details to inspect authentication state, source, and reset time when needed.

Draft instructions and attachments remain available until the task is created or the creation screen is explicitly closed. You can inspect Settings or another screen and return to **New task** without re-entering them. This draft does not persist after the app quits.

A temporary folder is deleted automatically when the task is completed. In a project, the task starts in the selected working folder. Supported agents can also use other folders in that project, so include only folders the task is allowed to access.

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
  --project Example

cat instruction.md | cockpit task create --stdin --project Example
cockpit task create --instruction-file instruction.md --project Example --directory /path/to/project/folder
```

Use `--stdin` or `--instruction-file` for multiline content or text containing backticks, quotes, `$`, or code fences. `--project` accepts an ID or exact name and uses the primary folder unless `--directory` selects another folder in that project. If both are omitted, the task starts in an operating-system temporary folder.

## Read next

- Organize multiple pieces of work: [Task list](https://agi-labo.com/en/tools/cockpit/docs/tasks)
- Send follow-ups to selected work: [Task details](https://agi-labo.com/en/tools/cockpit/docs/task-details)
- Review diffs and files: [Results and tools](https://agi-labo.com/en/tools/cockpit/docs/results-and-tools)
