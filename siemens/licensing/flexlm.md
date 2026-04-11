---
title: Siemens SALT / FlexLM License Server
description: Shared license server for all Siemens products - NX, Process Simulate, TIA Portal
published: true
date: 2026-04-11T22:07:54.903Z
tags: 
editor: markdown
dateCreated: 2026-04-11T22:07:54.903Z
---

# Siemens SALT / FlexLM License Server

**Path:** `/siemens/licensing/flexlm`  
**Tags:** `siemens`, `license`, `flexlm`, `salt`, `infrastructure`

This is the shared license server for all Siemens products in the company — NX, Process Simulate, TIA Portal, and any other Siemens software requiring a network license. All product pages link here instead of duplicating this information.

---

## Overview

Siemens uses **SALT (Siemens Advanced Licensing Technology)** which is built on top of FlexLM/FlexNet. It is backward compatible with older Siemens/Mentor vendor daemons (`mgcld`, `ugslmd`). The new unified vendor daemon is called `saltd`.

| Item | Detail |
|---|---|
| License type | Floating (network) |
| Default license port | `29000` |
| Default vendor daemon port | `29001` |
| Windows install path | `C:\Program Files\Siemens\License Server` |
| Linux install path | `/opt/Siemens/LicenseServer` |
| License file location (Windows) | `C:\ProgramData\Siemens\License Server\ActiveLicenses\` |
| Environment variable | `SALT_LICENSE_SERVER` |
| Admin tool | LMTOOLS (`lmtools.exe`) |

> **⚠ License server host TBD** — decision pending on whether to use a dedicated Windows Server or an existing workstation. Update this page once decided. See [Considerations](#server-host-considerations) below.

---

## Prerequisites

Before installing:

- [ ] Decide on server host machine (see [below](#server-host-considerations))
- [ ] Obtain the license file (`.lic`) from Siemens — requires the server's **MAC address**
- [ ] Have the **Siemens account (webkey/email)** ready for SLIM setup
- [ ] Ensure ports `29000` and `29001` are open on the server firewall
- [ ] Have administrator/root access on the server machine
- [ ] Download the installer: `SiemensLicenseServer_vX.X.X.X_Win64_x86-64.exe` (Windows) or `_Lnx64` (Linux) from Siemens Support Center

---

## Getting the Server MAC Address

The license file is tied to the server's MAC address. Get it before requesting the license file from Siemens.

**Windows:**
```cmd
ipconfig /all
```
Look for `Physical Address` under the main Ethernet adapter (not Wi-Fi, not virtual adapters).

**Linux:**
```bash
ip link show
```
Look for `link/ether` on your primary ethernet interface (e.g. `eth0`, `ens192`).

---

## Installation

### Windows

1. Run the installer as **Administrator**:
   ```
   SiemensLicenseServer_vX.X.X.X_Win64_x86-64.exe
   ```
2. Accept defaults for install path (`C:\Program Files\Siemens\License Server`)
3. When prompted, import your `.lic` license file
4. Set ports if needed (default `29000` / `29001` — only change if there is a conflict)
5. When prompted for **SLIM**, enter your Siemens webkey email — this installs the Siemens License Install Manager for future license management
6. Complete install — the `saltd` service starts automatically

### Linux

1. Run the installer with root privileges:
   ```bash
   sudo ./SiemensLicenseServer_vX.X.X.X_Lnx64.bin
   ```
2. Follow the same steps as Windows above
3. Verify the service is running:
   ```bash
   systemctl status saltd
   ```

---

## Configuring the License File

The installer handles most of this automatically. If you ever need to manually edit the `.lic` file:

1. Open the license file in a text editor
2. The top of the file has a `SERVER` line — ensure the hostname matches your server:
   ```
   SERVER your-server-hostname MACADDRESS 29000
   ```
3. The `DAEMON` line should point to the `saltd` daemon path
4. **Do not edit anything else** — all other values are used to compute the license key and will invalidate it

---

## Firewall Rules

Open these ports on the **server** machine:

| Port | Protocol | Purpose |
|---|---|---|
| `29000` | TCP | License server (lmgrd) |
| `29001` | TCP | Vendor daemon (saltd) |

On Windows Server — add inbound rules via Windows Defender Firewall.  
On Linux — add via `firewalld` or `ufw` depending on distro.

---

## Pointing Clients to the License Server

On each **client machine** that runs Siemens software, set the environment variable:

**Windows** (System Environment Variables):
```
Variable: SALT_LICENSE_SERVER
Value:    29000@your-server-hostname
```

**Linux** (add to `/etc/environment` or product-specific config):
```bash
export SALT_LICENSE_SERVER=29000@your-server-hostname
```

> Replace `your-server-hostname` with the actual server hostname or IP. Using hostname is preferred over IP so that it survives server IP changes.

---

## Verifying the Server

Use **LMTOOLS** (`lmtools.exe` on Windows, found in the install directory) to verify:

1. Open LMTOOLS
2. Go to **Server Status** tab
3. Click **Perform Status Enquiry**
4. You should see `saltd` listed with available license features

---

## Server Host Considerations

> **Decision pending** — update this section once confirmed.

| Option | Pros | Cons |
|---|---|---|
| Dedicated Windows Server | Stable, always on, no user dependency | Extra hardware/VM cost |
| Existing workstation | No extra cost | Goes offline when user shuts down — blocks everyone |

**Recommendation:** Even a low-spec VM running Windows Server or Linux is better than a workstation. All Siemens clients depend on this machine being up.

---

## Troubleshooting

**"Cannot connect to license server"**
- Check the server is running: open LMTOOLS → Server Status
- Check firewall ports `29000` and `29001` are open
- Check `SALT_LICENSE_SERVER` env variable is set correctly on the client
- Try `ping your-server-hostname` from the client to confirm network connectivity

**"Licensed number of users already reached"**
- All license seats are in use
- Use LMTOOLS → Server Status to see who has licenses checked out
- Contact IT admin to review license count vs user count

**"No such feature exists"**
- The product you are trying to run is not covered by the current license file
- Contact Siemens or your account manager to verify entitlements

**License server service not starting after reboot**
- On Windows: check Services (`services.msc`) — `Siemens License Server` should be set to Automatic
- On Linux: `sudo systemctl enable saltd`

---

## Related Pages

- [Siemens NX](/siemens/nx)
- [Siemens Process Simulate](/siemens/process-simulate)
- [Siemens TIA Portal](/siemens/tia-portal)
- [Infrastructure](/internal/infrastructure)