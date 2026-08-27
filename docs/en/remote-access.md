<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Remote access

Use Tailscale and HTTPS to supervise AGI Cockpit tasks, Asks, and Autoruns safely from a phone or another computer.

> Verified with AGI Cockpit 4.61.0 on 2026-08-27. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/remote-access)

Remote access lets you connect to the computer running AGI Cockpit from a phone, tablet, or another computer. This guide uses the recommended Tailscale and HTTPS configuration and ends with a working task view in the PWA.

## How remote access works

The computer running Cockpit becomes the connection target. The PWA in your browser operates that Cockpit. Tasks, agent processes, and working files remain on the target computer; they are not moved to the PWA.

| Mode | Who can connect | Transport |
| --- | --- | --- |
| Off | Desktop on this computer only | No remote connection |
| Tailscale only | Devices in the same tailnet | Tailscale's encrypted network, with optional HTTPS |
| Allow local Wi-Fi too | Devices on the same LAN | HTTP without encryption |

The AGI Cockpit process and remote access must remain running on the target computer. If you run Cockpit on several computers, each one remains a separate target with its own tasks and settings.

## Before you begin

| Requirement | What to confirm |
| --- | --- |
| AGI Labo membership | Desktop is signed in to an active member account |
| Tailscale | It is installed on the target and remote devices, and both are connected to the same tailnet |
| Tailscale administration | An Owner, Admin, or Network admin is available when HTTPS must be enabled for the first time |
| Browser | The remote device uses a current version of Safari, Chrome, Edge, or another modern browser |

If you do not use Tailscale yet, [install Tailscale](https://tailscale.com/download) and sign in to the same tailnet on both devices.

## Connect with Tailscale and HTTPS

### 1. Start remote access in Tailscale-only mode

1. Open **Remote access** in the lower-left corner of Desktop.
2. If Cockpit reports that Tailscale is stopped or needs authentication, start it or sign in from the on-screen action.
3. Select **Tailscale only**.
4. Confirm that **Connection info** shows a URL and QR code.

Tailscale-only mode rejects connections that do not come through Tailscale. Tailscale encrypts device-to-device traffic even while the displayed URL uses HTTP, but the browser still treats that page as an HTTP origin. Set up the certificate below to avoid browser warnings and support features that require HTTPS.

### 2. Get an HTTPS certificate

1. Select **Get certificate** in the Remote access panel.
2. If Cockpit shows `your Tailscale account does not support getting TLS certs`, open [Tailscale DNS settings](https://login.tailscale.com/admin/dns).
3. Enable MagicDNS in the Tailscale admin console.
4. Under **HTTPS Certificates**, select **Enable HTTPS** and review the disclosure before confirming.
5. Return to Cockpit and get the certificate again.
6. Confirm that Cockpit shows **HTTPS certificate issued** and a domain.

You can also open the [Tailscale DNS settings](https://login.tailscale.com/admin/dns) directly. If you cannot change them, ask an Owner, Admin, or Network admin of the target tailnet.

Enabling Tailscale HTTPS records the device name and tailnet DNS name from the certificate in the public Certificate Transparency log. Rename a device before requesting its certificate if its name contains sensitive information. See [Tailscale's HTTPS documentation](https://tailscale.com/docs/how-to/set-up-https-certificates) for details.

### 3. Turn on HTTPS and open the PWA

1. Turn on **HTTPS**.
2. Confirm that the connection URL now has the form `https://device-name.tailnet-name.ts.net:47280`.
3. Scan the QR code or open the displayed URL on the remote device.
4. If device pairing appears, enter the six-digit pairing code from the Desktop Remote access panel.

A Tailscale device owned by the same user in the same tailnet may be authenticated automatically from its Tailscale identity. A connection that Cockpit cannot authenticate automatically requires the pairing code. The code refreshes after five minutes; select **Regenerate** to replace it immediately.

The connection is ready when the PWA header shows the target and **Connected**, and you can open **Confirm**, **Tasks**, and **Autorun**.

## Supervise work in the PWA

| Screen | Main actions |
| --- | --- |
| Confirm | Review pending Asks and answer with choices, free text, or attachments |
| Tasks | Create tasks, review the list and details, send follow-ups or queue the next message, answer permissions or questions, complete tasks, and delete tasks |
| Autorun | Create, edit, enable, run now, delete, and review Autoruns |

The PWA is an operating surface for checking progress and decisions from another location. Enable remote access and configure its transport and HTTPS certificate in Desktop. Not every Desktop setting or result view is available in the PWA.

A message sent while a task is running enters the queue without stopping the current turn and is delivered automatically after the response. In Claude, Codex, and Grok Build Native UI, sending only `/compact` compacts the conversation context. A normal message sent during compaction is also queued. In an unsupported Native UI, `/compact` reports that it is unavailable instead of sending the text as a normal message.

Before adding AGI Cockpit to the home screen, turn on **HTTPS** in Desktop and open the `https://...` URL shown in **Connection info**. An HTTP URL is not a supported configuration for using AGI Cockpit as a PWA. After opening the HTTPS page, select **Add to Home Screen** from Safari's Share menu on iPhone or iPad. In Chrome on Android, open the browser menu and select **Install app** or **Add to Home screen**.

## Switch between Cockpit computers

If several computers have remote access enabled, select the connection target in the PWA header to see other Cockpit computers discovered through Tailscale. Select **Open** to move to another target or **Scan again** to refresh the list.

Each Cockpit remains independent. Switching targets does not merge or move tasks, Autoruns, settings, or working files between computers.

## Connect over local Wi-Fi

When Tailscale is unavailable and you need a temporary connection on a trusted LAN, select **Allow local Wi-Fi too**. Accept the risk in the confirmation, open the displayed `http://local-IP:47280` URL, and enter the six-digit pairing code.

Local Wi-Fi mode cannot use HTTPS. A third party on the same Wi-Fi network may be able to read the token and task content. Do not use this mode on public Wi-Fi, a shared office network, or a network available to visitors. Do not expose it to the internet with router port forwarding.

## Stop remote access

Select **Off** in the Desktop Remote access panel. Cockpit disconnects active sessions and returns the target to local-only use. After you enable it again, a device that is not authenticated automatically must use the new pairing code.

Cockpit also stops a running remote-access server and returns to Off if the AGI Labo sign-in or membership becomes invalid. A temporary membership-verification outage preserves a running connection and shows an action to check again instead of stopping it immediately.

## Inspect and configure it from the CLI

An AI agent or terminal can inspect the current state and prerequisites:

```bash
cockpit remote-access status --verbose
cockpit remote-access tailscale status
cockpit remote-access certificate status
```

To configure a stopped Cockpit for Tailscale and HTTPS, get the certificate, save the configuration, and enable remote access:

```bash
cockpit remote-access certificate generate
cockpit remote-access configure --scope tailscale --https true
cockpit remote-access enable
```

Configuration cannot change while the server is running. If you need to change it, confirm that active sessions may end, then run `cockpit remote-access disable --confirm`. CLI configuration and enablement for local-network access both require `--confirm-local-network`.

## Troubleshooting

| Message or state | What to check |
| --- | --- |
| Remote access shows a lock | Sign in to Desktop with an active AGI Labo member account |
| Tailscale is missing, stopped, or signed out | Install, start, or sign in from the on-screen action. Connect the remote device to the same tailnet too |
| `your Tailscale account does not support getting TLS certs` | Enable MagicDNS and HTTPS Certificates in [Tailscale DNS settings](https://login.tailscale.com/admin/dns), then get the certificate again in Cockpit |
| You cannot change the Tailscale settings | Ask a tailnet Owner, Admin, or Network admin |
| A certificate is issued but the URL is HTTP | Turn on **HTTPS** and confirm that Connection info changes to an HTTPS URL |
| No URL or QR code appears | Confirm that **Tailscale only** or **Allow local Wi-Fi too** is selected and the target shows as running |
| Cockpit rejects the pairing code | Use the latest code within five minutes. After **Regenerate**, enter the new code. After three failed attempts, wait 30 seconds |
| The PWA shows **Disconnected** | Confirm that Cockpit, remote access, and Tailscale are running on the target, then select **Retry now** |
| Another Cockpit is missing | Enable remote access on each computer, connect them to the same tailnet, then select **Scan again** |
| Cockpit cannot verify membership | Check the network connection and select **Check membership again** |

Tailscale itself can be connected while Cockpit is unable to read the Tailscale CLI state. If the CLI check error persists, reinstall Tailscale and check again.

## Security notes

- Use **Tailscale only** with HTTPS for normal remote access.
- In Tailscale access controls, allow only the people and devices that should reach Cockpit.
- The PWA can send task instructions, answer Asks, and change Autoruns. Lock connected devices and do not leave credentials on a shared device.
- Before requesting an HTTPS certificate, confirm that the device name is safe to include in a public certificate log.
- Local Wi-Fi mode is not encrypted. Limit it to a trusted network and the time you need it, then turn remote access off.

## Related pages

- [What is AGI Cockpit?](https://agi-labo.com/en/tools/cockpit/docs)
- [Task list and task details](https://agi-labo.com/en/tools/cockpit/docs/tasks)
- [Ask](https://agi-labo.com/en/tools/cockpit/docs/ask)
- [Autorun](https://agi-labo.com/en/tools/cockpit/docs/autorun)
