<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# App Surface

Attach an already-running Android target or iOS Simulator to a task, then safely observe, operate, recover, and detach it.

> Verified with AGI Cockpit 4.61.0 on 2026-08-27. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/app-surface)

App Surface attaches an already-running Android emulator, Android physical device, or booted iOS Simulator to one task so a person and agent can inspect and operate the same screen.

Cockpit does not boot or shut down the target, and it does not install, launch, or terminate its app. One target cannot be attached to multiple tasks at once.

## Prepare a supported target

For Android, make ADB available and either run an emulator or connect a physical device with USB or Wi-Fi debugging enabled. If the target is `unauthorized`, answer the debugging prompt on the device.

For iOS, install Xcode and boot the intended Simulator before discovery. A shut-down Simulator is `offline`, and Cockpit never boots it automatically.

```bash
cockpit app doctor
cockpit app targets
```

`doctor` checks ADB, Xcode, bundled control-runtime integrity, online targets, and the current attachment. It returns `nextAction` when recovery requires an external command. `targets` reports platform, emulator, Simulator, or physical-device kind, transport, online, offline, or unauthorized state, capabilities, and the owning task when busy.

## Attach it to the task

```bash
cockpit app attach <target-id>
cockpit app status
cockpit app show
```

`attach` completes an initial runtime and accessibility observation before returning. The first attachment to an Android physical device requires explicit approval through Cockpit Ask. Emulators and Simulators do not require approval.

Inspect `status.health` for target, mirror, accessibility, input, keyboard, foreground app, the latest structured error, and operability. `show` and `hide` affect only the side panel; mirroring and attachment continue while hidden.

## Observe the screen

```bash
cockpit app snapshot
cockpit app screenshot --output ./artifacts/app.png
cockpit app wait --text "Welcome" --timeout 10000 --require
cockpit app wait --label "Continue" --role button
```

`snapshot` returns the accessibility tree and temporary references such as `@e1`. Any tap, fill, swipe, or key action invalidates those references, so take a new snapshot before the next reference-based action.

`screenshot` returns a PNG and reports `coordinateSpace` and `screenshotScale` in its viewport. `wait` can combine text, elements, attachment status, Android package, and keyboard state. A normal timeout returns `timedOut: true`; `--require` turns it into `condition_not_met`.

## Prefer labels when operating

```bash
cockpit app tap --label "Continue" --role button --expect-text "Home"
cockpit app fill --label "Mission Notes" --role textbox $'Tokyo Tower\nSecond line'
cockpit app swipe up
cockpit app press back
cockpit app keyboard hide
```

Target by accessibility `--label` with an optional `--role`, then a reference from a fresh snapshot, then coordinates. Labels use case-sensitive exact matching by default. Use `--label-match prefix` or `contains` only when the platform appends context such as a placeholder. Zero matches fail with `element_not_found`; multiple matches fail with `ambiguous_element`.

Android coordinates are screen pixels and match screenshot pixels. iOS Simulator actions use logical screen points; multiply by `screenshotScale` to locate the same point in the PNG.

`dispatched: true` means the platform accepted the input, not that the app reacted. Add `--expect-text` or `--expect-not-text` to tap, swipe, and press, or take a fresh snapshot afterward.

`fill` reads a fresh accessibility snapshot and requires an exact value match, including Unicode and newlines. Secure fields are masked and reject `fill` with `secure_field`. Hiding the keyboard also fails if Cockpit cannot verify that it is hidden.

## Detach and recover

```bash
cockpit app hide
cockpit app detach
```

An `offline` or `stale` Surface keeps its last frame but disables interaction. Use **Reconnect target** in Desktop or reconnect from the CLI, then inspect health. Completing or deleting the task, or using `detach`, releases the target without terminating its app.

Treat errors as safety boundaries. Do not bypass physical-device approval, fill secure fields, steal a busy target, or start or stop an app unless the user separately asked. When a failure includes an `actionId`, keep its JSON with the nearest screenshot for diagnosis.

## Related pages

- [Results and tools](https://agi-labo.com/en/tools/cockpit/docs/results-and-tools)
- [`cockpit app` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/app)
- [Ask](https://agi-labo.com/en/tools/cockpit/docs/ask)
- [Security and data](https://agi-labo.com/en/tools/cockpit/docs/security-and-data)
