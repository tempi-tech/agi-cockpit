<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Accounts and Auto

Register isolated agent accounts and use Auto selection, pinned profiles, live task switching, and usage-limit recovery safely.

> Verified with AGI Cockpit 4.68.0 on 2026-09-03. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/accounts)

AGI Cockpit account profiles isolate multiple sign-ins for the same agent provider and let tasks, Autoruns, and Fleet runs choose between them. Profiles are supported for Claude, Codex, Antigravity, Cursor, Qoder, and Grok Build.

## Understand default and named profiles

Each provider has a default account that uses its existing environment. You can add named profiles such as `work` and `personal`. A profile name is Cockpit's local selection label, not the AI service's plan name or email address.

An agent account profile is separate from a Browser Identity. An account profile isolates execution authentication for Claude, Codex, or another task agent. A Browser Identity isolates cookies, localStorage, and other website state in the in-app browser.

## Create and sign in to a profile

Open the target agent under Settings → Agents, add an account profile, and sign in to it. From the CLI, identify the provider with `--agent-type`.

```bash
cockpit accounts
cockpit accounts create work --agent-type codex
cockpit accounts login work --agent-type codex
cockpit accounts list --agent-type codex
```

The list reports the provider, profile ID, name, email when available, sign-in state, provider-specific usage, and the time that usage was fetched. `auth_required` means the profile must sign in again. `error` means usage could not be fetched. Neither state means that usage is exactly zero, but Auto may be unable to select that profile safely.

Named Antigravity profiles use browser-based Google OAuth. OAuth tokens, conversations, logs, and usage history remain in profile-specific storage without falling back to a shared keyring.

## Use Auto

When account selection is available, new tasks, Autoruns, and Fleet nodes default to **Auto**. Auto is a selection method, not one named profile. At runtime it compares available usage across signed-in accounts and chooses an execution account.

Auto first requires remaining capacity in every applicable window, including shorter limits. It then compares accounts by the window with the latest reset and favors the account with the most remaining percentage per minute until that reset. This helps consume an underused weekly or other long-term quota that expires sooner. Shorter windows remain an immediate availability gate; they do not override the long-term ranking.

If an Auto task reaches a supported usage or plan limit, Cockpit selects another available account, transfers the saved session, and continues. Exhausted Claude usage credits and Codex workspace credits are included in this recovery. When no alternative is available, usage cannot be confirmed, or switching or resuming fails, the task reports the reason and waits for a person.

An Autorun resolves Auto again on every execution. Fleet can spread parallel nodes across available accounts instead of concentrating all work on one profile. Actual concurrency still depends on provider plans, usage limits, and machine resources.

## Pin one account

Choose a named profile when audit, client, billing, or data-separation requirements must pin the execution account. Use `--account` from the CLI.

```bash
cockpit task create --instruction "Review this change" --directory /path/to/repo --agent-type codex --account work
cockpit autorun create --name "Daily review" --instruction "Review changes" --directory /path/to/repo --agent-type codex --account work --type cron --expression "0 9 * * *"
```

A pinned profile does not switch automatically after a usage limit. If a saved fixed profile becomes unavailable, Cockpit does not silently substitute another account. It disables the Autorun and marks it as needing attention.

## Switch an active task

```bash
cockpit task account <task-id>
cockpit task account <task-id> work
cockpit task account <task-id> default
```

Claude, Codex, Grok Build, Antigravity, Cursor, and Qoder tasks can switch accounts while active. Cockpit stops the current runtime, transfers the saved conversation to the selected profile, and resumes the same task. A task may reject switching while it is generating a reply or immediately after startup, so wait until it can accept input.

After switching a task that stopped at a usage limit, send a follow-up to continue. Switching does not merge provider-side billing or unrelated conversation histories.

## Remove a profile

```bash
cockpit accounts remove work --agent-type codex --confirm
```

The default account cannot be removed. Removing a named profile requires confirmation and is rejected while a task or Autorun uses it. Change those consumers to Auto, default, or another profile first. Removal makes that profile's isolated authentication and saved data unavailable, so verify both the provider and name.

See [Agents and models](https://agi-labo.com/en/tools/cockpit/docs/agents-and-models) for capability differences, [Task details](https://agi-labo.com/en/tools/cockpit/docs/task-details) for stopped-task handling, the [`cockpit accounts` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/accounts) for the exact command contract, and [Security and data](https://agi-labo.com/en/tools/cockpit/docs/security-and-data) for credential boundaries.
