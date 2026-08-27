<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Update AGI Cockpit

Check and apply AGI Cockpit updates on Windows, macOS, or Linux, then inspect update state and diagnostic logs if an update fails.

> Verified with AGI Cockpit 4.61.0 on 2026-08-27. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/updates)

The update method depends on how AGI Cockpit is distributed. This guide identifies the method for your environment and verifies the result after updating.

## 1. Check the update method

Open the update notice in the lower-left corner or select **Updates** from the app menu. From the CLI, inspect the current update capability and state with:

```bash
cockpit update status
```

| Environment | Update method |
| --- | --- |
| Packaged macOS build | Cockpit checks, downloads, and installs the update |
| Packaged Linux build | Cockpit checks for the update; you replace the AppImage or `.deb` manually |
| Windows Store build | Update through Microsoft Store |
| Development or unsupported build | Find the appropriate package on the distribution page |

## 2. Apply the update

On macOS, use the on-screen action to restart and install after the download finishes. From the CLI, wait for the download, confirm that running tasks can end, then install:

```bash
cockpit update check --wait-download
cockpit update install --confirm
```

A successful `cockpit update install --confirm` restarts AGI Cockpit and terminates every running task.

On Linux, open the language-matched download page from **Update manually** or the update notice, then replace the AppImage or `.deb` with the package for the current CPU architecture. On Windows, update AGI Cockpit from the Microsoft Store library.

## 3. Verify the update

After restart, open **Updates** or run `cockpit update status` to inspect the current state and last install result. See [Release history](https://agi-labo.com/en/tools/cockpit/docs/releases) for the published changes.

## If the update fails

Cockpit retries an update check once after a temporary network or server failure. If it still fails, select **Try again** or **Update manually**. Cockpit does not continue with automatic installation when it cannot validate the update data.

On macOS, if the app has not quit within five minutes after an install request, Cockpit records the stalled handoff and retries the install request once. Diagnostic events for update checks, downloads, verification, installation, and restart are stored in `~/.agi-tools/data/cockpit/logs/updater.jsonl`.

Use `cockpit update status` to inspect the last error and install result. If the problem remains, follow the reporting guidance in [Reference and support](https://agi-labo.com/en/tools/cockpit/docs/reference-and-support).

## Related pages

- [`cockpit update` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/update)
- [Reference and support](https://agi-labo.com/en/tools/cockpit/docs/reference-and-support)
- [Release history](https://agi-labo.com/en/tools/cockpit/docs/releases)
- [Install AGI Cockpit](https://agi-labo.com/en/tools/cockpit/docs/getting-started)
