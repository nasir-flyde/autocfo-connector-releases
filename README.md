<div align="center">

<img src="https://img.shields.io/github/v/release/nasir-flyde/autocfo-connector-releases?label=Latest%20release&style=for-the-badge&color=0078D4" alt="Latest release">
<img src="https://img.shields.io/badge/Platform-Windows%2010%2F11%20(64--bit)-0078D4?style=for-the-badge&logo=windows" alt="Platform">
<img src="https://img.shields.io/badge/Tally-TallyPrime-FF6600?style=for-the-badge" alt="Tally">

# AutoCFO Connector

**Connects TallyPrime on your computer to AutoCFO.**

No static IP, no open ports, no firewall changes.

[**Download the latest version →**](https://github.com/nasir-flyde/autocfo-connector-releases/releases/latest/download/autocfo-connector-setup.exe) · [**What's changed →**](../../releases)

</div>

---

## What is the AutoCFO Connector?

The AutoCFO Connector is a small Windows service for the computer where TallyPrime runs. It connects **out** to AutoCFO over HTTPS and asks for work. When AutoCFO needs something from Tally, such as a company list, masters or vouchers, the connector asks TallyPrime on that computer and sends the answer back.

```
Your computer                                        AutoCFO
──────────────────────────────────────────           ─────────────
  TallyPrime  ◄── localhost:9000 ──►  Connector ── HTTPS (443) ──►  AutoCFO
                                      (Windows service)
```

> The connector never listens on a port. Every connection starts from your computer and goes out on port 443.

---

## Features

- **No network setup.** It works behind NAT, a VPN or a strict firewall, because it only makes outgoing HTTPS requests.
- **Reconnects by itself.** If the internet or Tally drops, it keeps trying and carries on once they're back.
- **Runs as a Windows service.** It starts with Windows and runs even when nobody is signed in.
- **Safe link.** The link uses a one-time pairing code. AutoCFO stores only a hash of the computer's key, and you can revoke a computer at any time.
- **Several companies.** It can work with any company open in TallyPrime.
- **One program.** No Node.js, .NET or other runtime to install.

---

## System requirements

| Requirement | Minimum |
|-------------|---------|
| Windows | Windows 10 or 11, 64-bit |
| Tally | TallyPrime, with its XML port turned on (usually 9000) |
| Internet | Outgoing HTTPS on port 443 |
| Rights | An administrator account to install |
| Disk | 200 MB |

---

## Installation

### Step 1: Get a pairing code

In AutoCFO, open the **Connect Tally** step, or go to **Settings → Connectors**, and click **Get pairing code**. You get a code like `ABCD-2345`. It works once and expires after a few minutes, so get it just before you install.

### Step 2: Download the setup program

Download [**`autocfo-connector-setup.exe`**](https://github.com/nasir-flyde/autocfo-connector-releases/releases/latest/download/autocfo-connector-setup.exe) on the computer that runs TallyPrime.

### Step 3: Run it

1. Run `autocfo-connector-setup.exe` and click **Yes** when Windows asks for administrator rights.
2. Click **Next** until it reaches **Link to AutoCFO**.

### Step 4: Enter your details

| Field | What to enter |
|-------|---------------|
| Pairing code | The code from step 1 |
| Tally address | Leave it as `http://localhost:9000` unless TallyPrime uses another port or runs on another computer |
| Computer name | Optional. This is how the computer appears in AutoCFO; it defaults to the Windows computer name |

Click **Next**. The connector links to AutoCFO, checks that it can reach TallyPrime and starts the service. If TallyPrime isn't open yet, setup tells you and finishes anyway; the connector connects once TallyPrime is open.

From then on the connector runs in the background and starts with Windows.

### Installing on many computers

IT teams can install without the wizard:

```
autocfo-connector-setup.exe /S /CODE=ABCD-2345 /TALLY=http://localhost:9000 /NAME=ACCOUNTS-PC
```

`/TALLY` and `/NAME` are optional. Each computer needs its own pairing code. Setup exits with code 3 if the code was refused.

---

## Checking it works

In AutoCFO, open **Settings → Connectors**. Your computer should show as **Online** within a minute, with the companies open in TallyPrime.

You can also check the Windows service:

1. Press `Win + R`, type `services.msc` and press Enter.
2. Find **AutoCFO Connector**. Its status should be **Running**.

---

## Changing the settings

To change the Tally address, or link the computer again with a new code:

**Start menu → AutoCFO Connector → Configure AutoCFO Connector**

It opens the same **Link to AutoCFO** page. You don't need to uninstall, and the service picks up the change within a few seconds.

To update the connector, run the latest setup program over the old one. It keeps the link.

---

## Where things are

| Path | What it holds |
|------|---------------|
| `C:\Program Files\AutoCFO Connector\autocfo-connector.exe` | The connector |
| `C:\Program Files\AutoCFO Connector\Configure.exe` | Configure AutoCFO Connector |
| `C:\ProgramData\AutoCFO Connector\config.json` | The link and the Tally address. Only administrators and Windows can read it |
| `C:\ProgramData\AutoCFO Connector\logs\` | Logs (Start menu → AutoCFO Connector → AutoCFO Connector logs) |
| Windows service `AutoCFOConnector` | Shown as **AutoCFO Connector**; starts automatically |

---

## Uninstalling

Go to **Settings → Apps → AutoCFO Connector → Uninstall**.

The uninstaller stops and removes the service, revokes the computer's link in AutoCFO, and deletes `C:\ProgramData\AutoCFO Connector`. If it can't reach AutoCFO, it tells you; revoke the computer yourself in **Settings → Connectors**.

---

## Troubleshooting

<details>
<summary><strong>The computer shows as Offline in AutoCFO</strong></summary>

- Open `services.msc` and check that **AutoCFO Connector** is **Running**. If not, start it.
- Check that your firewall or proxy allows outgoing HTTPS on port 443.
- Look at the newest file in the logs folder (Start menu → AutoCFO Connector → AutoCFO Connector logs).
- If the logs say AutoCFO no longer accepts the link, open **Configure AutoCFO Connector** and link again with a new code.

</details>

<details>
<summary><strong>"Can't reach Tally"</strong></summary>

- Make sure TallyPrime is open with a company loaded.
- In TallyPrime, press **F1 → Settings → Connectivity** and set **TallyPrime acts as** to **Server** or **Both**, with the port you entered (usually **9000**).
- Open **Configure AutoCFO Connector** and check that the Tally address uses the same port.

</details>

<details>
<summary><strong>"That pairing code is wrong or has expired"</strong></summary>

- Codes work once and expire after a few minutes. Get a new one in AutoCFO under **Settings → Connectors** and enter it straight away.
- Type it exactly as shown. The dash is optional.

</details>

<details>
<summary><strong>Windows SmartScreen warns about the setup program</strong></summary>

Click **More info → Run anyway**. New versions can trigger SmartScreen until Windows has seen them on enough computers. You can check the download against `SHA256SUMS` on the release page:

```
certutil -hashfile autocfo-connector-setup.exe SHA256
```

</details>

---

## Support

For help installing, or to report a problem, contact AutoCFO support and include the newest log file from the logs folder.
