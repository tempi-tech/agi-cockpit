<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Cockpit CLI

Learn how to connect to AGI Cockpit, inspect JSON results, supervise tasks, request decisions, and operate browser, app, Autorun, and Fleet surfaces.

> Verified with AGI Cockpit 4.61.0 on 2026-08-27. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli)

The `cockpit` CLI is the first-party control plane for tasks, surfaces, settings, automation, and local app operations. Commands return JSON so agents and scripts can verify identifiers, state, and errors without parsing screen text.

## Setup and connection

The packaged app installs a common launcher under `~/.agi-tools/bin`. A task started by Cockpit receives connection context automatically, so ordinary commands target the instance that owns the task. Run `cockpit doctor` to inspect the selected instance, runtime files, ports, listeners, and authentication results.

The CLI does not silently fall back to another Cockpit instance when the selected one is unavailable. Treat `instance_mismatch` as a target error and inspect the connection instead of resending the command elsewhere.

## Inspect JSON results

Every command returns a JSON object. Check `ok`, identifiers, state fields, and any command-specific result before continuing. A delivered input event is not proof that the target application accepted it; verify the intended postcondition.

Exit code 7 or `Cannot reach AGI Cockpit` means the app is not reachable or the installed CLI is too old for the fallback transport. It is not a filesystem-permission error. Use `cockpit doctor` to compare advertised endpoints with actual listeners.

## Start and supervise tasks

Use `cockpit task create` or `cockpit task run` to start work, `task get` and `task list` to inspect it, `task send` for follow-up instructions, and `task wait --since <seq>` for incremental reports. Use stdin or a file for multiline instructions rather than embedding them in a shell argument. See [Task management (CLI)](https://agi-labo.com/en/tools/cockpit/docs/task-management) for the full parent-child, reporting, follow-up, and completion flow.

Completion and deletion are separate. Completing a task can affect its temporary directory or Worktree, while deleting removes task history and associated local state. Confirm the exact ID and required artifacts before destructive or bulk actions.

## Distinguish Ask, display, and HTML

`cockpit ask` waits for a human answer and resumes the same task after that answer. `cockpit display` presents information without waiting. `cockpit html` stores a generated HTML Surface for the task.

After creating an Ask, end the current turn and wait. Do not poll for the answer or continue work that depends on it. An Ask answer is not itself permission from the operating system or an external service.

## Operate browser and App Surfaces

Use `cockpit browser` for web pages and `cockpit app` for an already-running Android target or iOS Simulator. Confirm the Browser Identity, and verify browser clicks and submissions with postconditions such as URL, text, element state, or network activity. In App Surface, prefer accessibility labels and verify a fresh snapshot or explicit expectation after input.

External links, uploads, physical devices, and secret input have additional safety boundaries. Do not resend an already delivered action without checking state because it can create a duplicate operation.

See [cockpit browser](https://agi-labo.com/en/tools/cockpit/docs/browser), [Browser Identity](https://agi-labo.com/en/tools/cockpit/docs/browser-identities), and [App Surface](https://agi-labo.com/en/tools/cockpit/docs/app-surface) for practical workflows.

## Use Autorun and Fleet

`cockpit autorun` starts a new task or sends instructions to an existing task once, on an interval, or from cron. Membership is checked both when the Autorun is created and when it runs. If a saved runtime setting becomes unavailable, Cockpit disables the Autorun instead of silently choosing another runtime.

`cockpit fleet` executes dependency-aware tasks as a Run. It covers YAML validation, gates, retries, resume, Run titles, and node progress. Use Autorun for simple scheduled starts and Fleet for dependent multi-step work. See [Fleet](https://agi-labo.com/en/tools/cockpit/docs/fleet) for the practical workflow and the [`cockpit fleet` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/fleet) for exact syntax.

## Distinguish local and remote targets

Supported `task` and `autorun` commands can target a registered device alias or host through `--host`. Commands without host support, including browser and display operations, affect only the local Cockpit instance.

Stopping Remote Access, clearing Identity data, deleting an account, and uninstalling the CLI require explicit confirmation flags. A confirmation flag is not a substitute for user authorization; verify the requested action and exact target first.

## Command reference

The references below are generated at build time from `electron/cockpit-docs/help`, the same canonical source used by in-app `cockpit help [topic]`. Creative workflows are outside the scope of the official guides, but existing related commands remain in the generated reference to preserve CLI completeness.
