<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Hooks

Run actions in response to task completion, Asks, and hotkeys, and learn how to configure Hooks and inspect their run history.

> Verified with AGI Cockpit 4.74.0 on 2026-09-09. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/hooks)

Hooks automatically run a registered action when something happens in Cockpit, such as a task completing or an Ask being created. Each Hook saves a rule: when this event happens, run this action. A hotkey can also trigger a Hook.

## Choose between Hooks and Autorun

| Goal | Feature | Example |
| --- | --- | --- |
| Start work at a set time or interval | [Autorun](https://agi-labo.com/en/tools/cockpit/docs/autorun) | Create a report every morning at 9 |
| Run an action in response to an event | Hooks | Run a summary script when a task completes |
| Coordinate tasks with dependencies | [Fleet](https://agi-labo.com/en/tools/cockpit/docs/fleet) | Follow research with writing and review |

You can combine them. A Hook can react when a task started by Autorun completes, or run a follow-up action when an entire Fleet Run finishes.

## Choose a trigger

| Trigger | Common events | Example use |
| --- | --- | --- |
| A task changes state | `task.completed`, `task.waiting`, `task.error` | Run follow-up work or notify someone about a waiting or failed task |
| An Ask is created or answered | `ask.created`, `ask.resolved` | Connect decision requests and answers to an external service |
| An Autorun dispatches | `autorun.triggered` | Record a scheduled execution |
| A Fleet starts or finishes | `fleet.run.started`, `fleet.run.completed` | Run a follow-up action after a workflow finishes |
| The app starts or quits | `app.ready`, `app.quit` | Run a preparation script at startup |
| A shortcut is pressed | `hotkey` | Trigger a frequent action from the keyboard |

`task.completed` fires when a task transitions to completed. Saving the same status again does not fire it. `fleet.run.completed` also fires for failed and canceled Runs, so add a status filter if an action should run only on success.

## Ask an agent to configure a Hook

In a Cockpit task, describe the trigger, the target, and the action you want.

> Create a Hook that runs my summary script when a task in this project completes. Exclude child tasks. Register it as disabled first, then test it and check the result before enabling it.

External integrations need a script and authentication for the service they connect to. A Hook stores the actual command or script to execute.

## Register and test from the CLI

Use `cockpit hooks` to add, update, and test Hooks. This example creates a Hook that displays a message in Cockpit when a task completes. The shell syntax below is for macOS and Linux.

```bash
cockpit hooks add --event task.completed \
  --name completion-message \
  --disabled \
  --run 'cockpit display --text "${COCKPIT_TASK_NAME} completed"'
```

Replace `<hookId>` below with the returned `hook.id`. Replace `<taskId>` with a task ID from `cockpit task list`.

```bash
cockpit hooks test <hookId> --task <taskId>
cockpit hooks enable <hookId>
```

Check that the test returns `exitCode: 0` and displays the intended message before enabling the Hook. `test` executes the action even when the Hook is disabled. It does not validate event or filter matching and does not write to normal run history, so also check a real event and its history after enabling the Hook.

## Narrow the target and choose an action

Filter by project, task name, agent, or other fields. This example reacts to task completion under a directory and excludes child tasks. Replace the paths with your own.

```bash
cockpit hooks add --event task.completed \
  --name project-summary \
  --directory /path/to/project \
  --no-child \
  --run-file /path/to/project/scripts/summarize.sh \
  --disabled
```

Use `--run` for a short command and `--run-file` for a maintained script. When several filters are present, an event must match all of them. Scripts receive event data as JSON on standard input and through environment variables such as `COCKPIT_TASK_ID`.

## Settings and run history

1. Open **Settings → Hooks** from the app menu in the lower-left corner.
2. Find the registered Hook and enable or disable it as needed.
3. Expand its run history to inspect run times, events, exit codes, standard output, and standard error.
4. Remove a Hook when you no longer need it. Existing run history remains after removal.

You can also inspect status and history from the CLI.

```bash
cockpit hooks list
cockpit hooks runs --hook <hookId> --limit 20
cockpit hooks disable <hookId>
```

If a Hook does not run, check that it is enabled and that the event and filters match the intended target. For hotkeys, also check `registered` in `cockpit hooks get <hookId>`. A value of `false` means the shortcut is not registered. If an action fails, inspect its exit code and standard error. The default timeout is 60 seconds; change it with `--timeout`.

## Execution considerations

Registered actions run with your local permissions, independently of the agent's approval mode. Register commands and scripts whose behavior you have checked. All Hooks commands support `--host <host-or-alias>`. Remote registration and testing execute shell code on the target computer. A paired bearer token is required; Tailscale-only access additionally requires a verified peer or loopback connection. Peer trust alone does not authorize commands. The token grants control of the target instance, including Hook registration and execution. Script paths and directory filters refer to the target computer.

Cockpit waits at most five seconds for `app.quit` actions. Do not use them for long work that must finish before exit. Event chains have execution limits; when a Hook starts another task, narrow its filters to avoid repeatedly triggering itself.

## Related pages

- [Autorun](https://agi-labo.com/en/tools/cockpit/docs/autorun): automation based on times and intervals
- [Fleet](https://agi-labo.com/en/tools/cockpit/docs/fleet): task execution order and dependencies
- [`cockpit hooks` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/hooks): all events, filters, hotkeys, and execution controls
- [Security and data](https://agi-labo.com/en/tools/cockpit/docs/security-and-data): execution permissions and stored data
