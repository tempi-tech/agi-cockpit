<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Browser Identity

Isolate browser sign-ins by Identity, assign them to tasks and Autoruns, and safely import, clear, or remove their data.

> Verified with AGI Cockpit 4.61.0 on 2026-08-27. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/browser-identities)

A Browser Identity is a local persistent area that separates sign-in state and site data for the in-app browser. Use different Identities for different jobs, clients, or verification conditions when the same site needs separate accounts.

It is separate from an agent account profile. Browser Identity isolates website state; an agent profile isolates execution authentication for Claude, Codex, or another task agent.

## Data isolated by an Identity

Each Identity separates:

- Cookies and cache
- localStorage and site permissions
- Proxy authentication
- Browser sessions and navigation history
- Identity-scoped passkey and WebAuthn state

Popups and OAuth windows inherit their parent tab's Identity. Switching to another Identity never copies this data.

## Default Identity

Tasks and Autoruns without an explicit assignment use Default Identity. Default preserves the legacy `persist:in-app-browser` area and its existing sign-ins.

Default Identity can be identified by name and color but cannot be removed. To verify a public signed-out result, create a separate empty Identity instead of clearing Default's cookies.

```bash
cockpit browser identity create --name logged-out-check
cockpit task browser-identity logged-out-check
cockpit browser open https://example.com --browser-identity logged-out-check --json
```

## Create and inspect Identities

Open **Browser Identity** from the lower-left app menu to create, rename, recolor, clear, or remove an Identity. The equivalent CLI commands are:

```bash
cockpit browser identity list --in-use --json
cockpit browser identity get work --json
cockpit browser identity usages work --json
cockpit browser identity create --name work --color "#3B82F6" --json
cockpit browser identity update work --name client-a --color "#8B5CF6" --json
```

`list` reports usage counts and filters all, in-use, or unused Identities. `get` and `usages` return Identity metadata, counts, and the tasks, Autoruns, and browser sessions that reference it.

## Assign an Identity to a task

Each task has one Browser Identity. Select it while creating a task, or change an existing task from the browser side panel or CLI.

```bash
cockpit task browser-identity <taskId> work
cockpit task browser-identity <taskId>
```

Inside a task, omit the task ID to change the calling task. New sessions use the newly assigned Identity. Existing sessions remain under the original Identity and become available again after switching back.

`cockpit browser open ... --browser-identity work` only asserts that the assignment is work; it does not change it. A session or tab from another Identity is rejected with guidance to use the assignment command.

## Assign Identities to Autorun and Fleet

A new-task Autorun stores its Browser Identity assignment and passes it to every task it creates. An Autorun without an assignment uses Default. An existing-task Autorun does not change the target task's Identity.

Fleet can assign a Browser Identity to each task node. Give nodes explicit, purpose-specific Identities when they require different signed-in states instead of sharing one Identity.

## Import a Chrome session on macOS

On macOS, finish sign-in in Chrome, then import cookies for the selected tab's registrable domain and localStorage for its exact origin into the same Browser Identity.

```bash
cockpit browser import-session --browser-identity work --profile "Profile 2" --json
```

`--browser-identity` selects the AGI Cockpit destination; `--profile` selects the Chrome source profile. The first import may show a macOS Keychain prompt for **Chrome Safe Storage**.

The import does not transfer sessionStorage, IndexedDB, extension state, device-bound authentication, or the passkey itself. If the site remains signed out, sign in once inside that Identity's in-app browser. `import-cookies` is retained for compatibility but moves cookies only, so prefer `import-session`. Import is supported only on macOS.

## Clear data or remove an Identity

Clear and remove cannot be undone, and both require `--confirm`.

```bash
cockpit browser identity clear client-a --cookies --confirm --json
cockpit browser identity clear client-a --all --confirm --json
cockpit browser identity remove client-a --replace-with default --confirm --json
```

`clear` closes the Identity's live sessions before clearing exactly one of all data, cookies, or cache. `remove` clears every persistent data type and deletes the Identity itself.

An Identity referenced by an active task or Autorun cannot be removed without `--replace-with`. Cockpit reassigns those references first and rolls them back if removal fails. If only completed tasks reference the Identity, removing it without a replacement returns those references to Default.

Use `usages` to inspect affected tasks, Autoruns, and sessions before removal.

## Related pages

- [cockpit browser](https://agi-labo.com/en/tools/cockpit/docs/browser)
- [Autorun](https://agi-labo.com/en/tools/cockpit/docs/autorun)
- [Fleet](https://agi-labo.com/en/tools/cockpit/docs/fleet)
- [Security and data](https://agi-labo.com/en/tools/cockpit/docs/security-and-data)
