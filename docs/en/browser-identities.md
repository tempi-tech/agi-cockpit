<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Browser Identity

Isolate browser sign-ins by Identity, assign them to tasks and Autoruns, and safely import, clear, or remove their data.

> Verified with AGI Cockpit 4.77.0 on 2026-09-13. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/browser-identities)

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

Open **Browser** from the lower-left app menu. Choose **Add Identity** in the left sidebar to create one, or select an Identity to edit its name and color above its tab list. Choose **Save** to apply edits. Data clearing and deletion require confirmation; Default cannot be deleted. The equivalent CLI commands are:

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

Inside a task, omit the task ID to change the calling task. New sessions use the newly assigned Identity. Existing sessions remain under the original Identity. From Desktop Settings > Browser Tabs, you can reveal and operate existing tabs owned by that task. Agent and CLI access requires switching the task assignment back to the original Identity.

`cockpit browser open ... --browser-identity work` only asserts that the assignment is work; it does not change it. A session or tab from another Identity is rejected with guidance to use the assignment command.

## Assign Identities to Autorun and Fleet

A new-task Autorun stores its Browser Identity assignment and passes it to every task it creates. An Autorun without an assignment uses Default. An existing-task Autorun does not change the target task's Identity.

Fleet can assign a Browser Identity to each task node. Give nodes explicit, purpose-specific Identities when they require different signed-in states instead of sharing one Identity.

## Import a browser session on macOS

On macOS, finish sign-in in Chrome, Brave, Edge, Arc, Vivaldi, Opera, or Firefox, then import the supported state for the selected in-app tab into its Browser Identity. Inspect detected browsers and profiles first when you need an exact source.

```bash
cockpit browser import-sources --json
cockpit browser import-session --browser-identity work --browser brave --profile-id "Profile 2" --json
```

`--browser-identity` selects the AGI Cockpit destination. `--browser` selects the source browser, while `--profile-id` selects the discovered profile unambiguously; `--profile` can instead match a profile directory or Firefox profile name. With no source option, Cockpit chooses the most recently used preferred profile across the detected browsers.

Chromium sources import cookies for the tab's registrable domain and localStorage for its exact origin. Each browser has a separate **Safe Storage** Keychain item, so its first applicable import may show a macOS permission prompt. Firefox imports cookies only and does not access Keychain; the result reports that localStorage was unsupported.

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
