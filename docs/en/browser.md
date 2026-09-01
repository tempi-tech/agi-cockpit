<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# cockpit browser

Open web pages in a task's in-app browser so people and agents can safely inspect, operate, and verify the same tabs.

> Verified with AGI Cockpit 4.67.0 on 2026-09-02. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/browser)

`cockpit browser` is the official surface for opening real web pages in a task-scoped in-app browser and inspecting their DOM, appearance, and outcomes. It is driveable, not just viewable: it can click, type, select, upload, paste, press keys, and scroll.

[HTML Surface](https://agi-labo.com/en/tools/cockpit/docs/results-and-tools#distinguish-html-mode-from-html-surface) displays reports produced by an agent. Use the in-app browser for external navigation, sign-in, form operation, and Web UI verification.

## Share one page between person and agent

Each tab has one canonical page instance shared by the person viewing the side panel and the agent operating it. Switching tasks, tabs, or panels detaches the page from the window without reloading it, preserving unsent forms, scroll position, SPA state, and popup or OAuth context.

Browser sessions and navigation history survive task completion, resume, and app restart. Form values and scroll position are restored after restart on a best-effort basis, and saved state is encrypted when the operating-system credential store is available.

After an app restart, a saved tab that has not been used stays unloaded without a renderer until it is needed. `loaded: false` together with `rendererResponsive: false` in `tabs` means unloaded, not stuck. Selecting that tab, showing it in the side panel, or targeting its tab ID with a command loads it.

A parked tab can receive input while hidden without showing a separate window or switching macOS Spaces. The same page is visible when the panel is shown again.

## Open a page

```bash
cockpit browser open https://example.com --json
cockpit browser tabs --summary --json
cockpit browser show --json
```

Inside a Cockpit task, omitting a session or task ID uses the current task. `open` reuses its most recent session and selected tab. Add `--new-tab` when the current page has unsent state to preserve.

```bash
cockpit browser open https://example.com/form --new-tab --json
cockpit browser goto <tabId> https://example.com/next --json
cockpit browser reload <tabId> --json
```

`tabs --summary` reports selection, a body-text preview, loading time, renderer responsiveness, and the latest navigation error. Popups and page-side `window.close()` are reflected in Cockpit's tabs.

## Choose snapshot or screenshot

```bash
cockpit browser snapshot <tabId> --json
cockpit browser snapshot <tabId> --wait-for-text "Ready" --timeout 10000 --json
cockpit browser screenshot <tabId> --full-page --output ./page.png --json
```

`snapshot` returns rendered text, links, buttons, inputs, roles, accessible names, and selectors. Use it to identify controls and verify machine-readable state. Use `screenshot` for visual layout, imagery, canvas content, or a result a person needs to see.

Locators traverse open shadow roots. A normal CSS selector works across them, and `host >>> inner` pins an explicit shadow boundary. Closed shadow roots are inaccessible from the page; use screenshot coordinates when necessary.

## Evaluate JavaScript in the main frame

Use `evaluate` to inspect framework state, `localStorage`, element properties, and other values that are not rendered in the DOM.

```bash
cockpit browser evaluate <tabId> --expression "document.title"
cockpit browser evaluate <tabId> --expression "JSON.parse(localStorage.getItem('draft')).body" --json
cat inspect.js | cockpit browser evaluate <tabId> --stdin
```

JavaScript runs only in the tab's main frame and never enters an iframe. Top-level `await` is supported, and a returned Promise is awaited. The result must be JSON-serializable; DOM nodes, functions, symbols, and cyclic objects cannot be returned. Evaluation carries no user gesture, so it cannot stand in for a person when a Web API requires one. Pass a longer script through `--expression-file` or `--stdin`.

## Operate by meaningful name

```bash
cockpit browser click <tabId> --role button --name "Submit" --json
cockpit browser type <tabId> --role textbox --name "Email" --text "name@example.com" --json
cockpit browser select <tabId> --selector "#plan" --option-text "Team" --json
cockpit browser scroll-into-view <tabId> --role button --name "Continue" --json
```

Prefer `--role` and `--name`, then a stable CSS selector, then viewport coordinates. Accessible names are computed from `aria-labelledby`, `aria-label`, labels, alt text, values, content, and titles. Matching is case-insensitive and exact by default; use `--name-match prefix|contains` explicitly when needed.

No match, several matches, and all-hidden matches fail with `browser_target_not_found`, `browser_target_ambiguous`, and `browser_target_not_visible`, respectively, and return candidate details. Cockpit never silently operates the first ambiguous candidate.

Before input, Cockpit scrolls the window and every scrollable ancestor on both axes to reveal the target. If the center of a requested child, such as text inside a large card link, does not land on the link, Cockpit keeps the nearest clickable ancestor and tries several safe hit points. It still refuses to click through an unrelated modal, banner, or other covering element and returns `browser_click_target_unreachable` with diagnostics for the candidate points.

## Verify operations with postconditions

```bash
cockpit browser click <tabId> --role button --name "Save" \
  --wait-for-text "Saved" --timeout 10000 --json

cockpit browser click <tabId> --selector "#checkout" \
  --wait-for-url "*/complete" \
  --wait-for-response-url "*/api/orders*" \
  --wait-for-response-status "200-299" --json
```

`changed` and `changeSignals` report observed changes to the URL, DOM, form values, and related state. `changed: false` is not proof of success. Add a postcondition that matches the requested outcome: URL, visible text, selector state, a new tab, or a network status.

If `eventDispatched: false` but `inputDelivered: true`, the operation may have taken effect even though event propagation was stopped. Avoid an immediate retry that could submit twice; inspect the current page with a snapshot first.

Network verification returns only method, status, and a URL with query values and the hash masked. It never returns request or response bodies, headers, or payloads.

## Type, paste, upload, and dismiss dialogs

```bash
cockpit browser paste <tabId> --selector "[contenteditable]" --text "Long content" --json
cockpit browser upload <tabId> --role button --name "Attach a file" --path ./build.zip --json
cockpit browser press <tabId> --selector "#search" --key Enter --json
cockpit browser dismiss-dialog <tabId> --button-text "OK" --json
```

`type` replaces the target value and verifies the final value. `paste` sends a real clipboard paste event, verifies that the requested text appeared, and restores the previous clipboard representations. `upload` sets a file input or a file input inside a dropzone without opening the operating-system picker.

`dismiss-dialog` handles a pending JavaScript dialog first, then a visible HTML dialog. Use `--reject` for the negative action. Postconditions do not expand authority: submitting, purchasing, publishing, deleting, and other external state changes remain subject to user approval.

## Recover loading and renderer failures

Use `health` from `tabs` to distinguish these cases:

| Value | Meaning |
| --- | --- |
| `loadingDurationMs` | How long Chromium has reported the page as loading |
| `rendererResponsive` | Whether the renderer answered a short JavaScript ping |
| `lastNavigationError` | The latest main-frame load failure |

Wait when the site is merely slow, and inspect the URL or connection after a navigation error. If the renderer is stuck, recreate only that renderer while keeping the same tab ID, URL, session, and Browser Identity.

```bash
cockpit browser tab recreate <tabId> --json
```

## Sign-in and safety boundaries

See [Browser Identity](https://agi-labo.com/en/tools/cockpit/docs/browser-identities) for cookie and localStorage isolation, task and Autorun assignment, Chrome session import, clearing, and removal.

Passkeys can use Touch ID in signed macOS builds and Windows Hello on Windows. On macOS, Cockpit can use only passkeys registered from the in-app browser; it cannot directly use passkeys previously registered in Safari, Chrome, or iCloud Keychain.

When no passkey matches Touch ID, the browser panel explains why and offers registration of a new passkey, password sign-in, and `cockpit browser import-session` as alternatives. `cockpit browser diagnostics` records the recent attempt as `no-matching-credential`. `import-session` transfers eligible Chrome sign-in state on macOS; it does not import the passkey itself.

Linux has no platform authenticator, so use a roaming security key or password. If no prompt appears, use `cockpit browser diagnostics` to inspect platform-authenticator state and the most recent WebAuthn attempt.

Only HTTP and HTTPS links can normally leave the in-app browser. A `mailto` link opens only after the person's own interaction and explicit confirmation; other schemes are rejected. Agent automation does not count as that person interaction.

## Related pages

- [Browser Identity](https://agi-labo.com/en/tools/cockpit/docs/browser-identities)
- [`cockpit browser` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/browser)
- [Results and tools](https://agi-labo.com/en/tools/cockpit/docs/results-and-tools)
- [Security and data](https://agi-labo.com/en/tools/cockpit/docs/security-and-data)
