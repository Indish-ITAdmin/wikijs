---
title: Siemens License Server
description: Contains installation, configuration and troubleshooting details
published: true
date: 2026-04-15T09:56:23.054Z
tags: license
editor: markdown
dateCreated: 2026-04-15T09:54:51.299Z
---

# Siemens License Server

## Overview

Siemens uses **SALT** (Siemens Advanced Licensing Technology), which is built on top of FlexLM/FlexNet. It is backward compatible with older Siemens/Mentor vendor daemons (`mgcld`, `ugslmd`, `cdlmd`). The new unified vendor daemon is called `saltd`. This document provides full details on the installation, configuration, and troubleshooting of Siemens floating licenses.

## Architecture Overview

Floating licenses use a client-server architecture. Although it may appear that both the client and server are separate machines, they can run on the same machine.

### Diagram

![screenshot_2026-04-15_145507.png](/attachments/screenshot_2026-04-15_145507.png)

### Components

1. **`saltd` process** — The main licensing process. It can be managed either via `lmutil.exe` (CLI) or `lmtools.exe` (graphical manager).

2. **License file** — Provided by Siemens after you supply a Composite ID (a string generated from the MAC address and hostname). After installation, the license file is located at:
   `C:\ProgramData\Siemens\License Server\Active Licenses\{ugslmd.lic,cdlmd.lic,saltd.lic}`

3. **`saltd.opt`** *(optional)* — An options file used to bind license features to specific users or hosts for granular control. If used, it must be placed in the same directory as the license file:
   `C:\ProgramData\Siemens\License Server\Active Licenses\saltd.opt`

4. **TCP Ports** — By default, the Siemens License Server uses ports `29000` and `29001/tcp`. Older installations may use `28000` and `28001/tcp`. Ports can be configured during installation.


## Prerequisites

1. **Valid license file**

   > Before installing, always run `getcid.exe` and verify that the CID it displays matches the CID in the license file. A mismatch will cause a **HOSTID mismatch** error during installation. Also ensure that the version in the license file is supported by the license server application. It is recommended to use the latest installer, as it is backward compatible.

2. **Windows Server 2022/2025, windows 11 pro, Ubuntu LTS/ RHEL (Refer seperate docs for linux here)**
3. **Administrator access**
4. **Firewall access: TCP ports 29000 and 29001 must be open; the installer can add Windows Firewall rules automatically, but network-level firewalls need manual configuration.**
5. **System time sync: Client and server clocks must be in sync, as a time mismatch can prevent license checkouts**


## Installation

1. Run `SiemensLicenseServer.exe` as Administrator.

2. Select the language and click **Next**.

![screenshot_2026-04-15_111713.png](/attachments/screenshot_2026-04-15_111713.png)*.

3. Browse to the license file and click **Next**.

![screenshot_2026-04-15_111725.png](/attachments/screenshot_2026-04-15_111725.png)*.
![screenshot_2026-04-15_111812.png](/attachments/screenshot_2026-04-15_111812.png)

4. On the port configuration screen, keep the default ports unless a different port is required. *(You can expand Advanced Settings to specify custom ports.)*

![screenshot_2026-04-15_111826.png](/attachments/screenshot_2026-04-15_111826.png)

5. Keep the destination folder at the default location under Program Files.

![screenshot_2026-04-15_111834.png](/attachments/screenshot_2026-04-15_111834.png)

6. Keep the logging settings at their defaults and click **Next**.

![screenshot_2026-04-15_111843.png](/attachments/screenshot_2026-04-15_111843.png)

7. Select **"I don't want this feature"** for the Siemens License Install Manager.

![screenshot_2026-04-15_111851.png](/attachments/screenshot_2026-04-15_111851.png)

8. Confirm the installation summary and click **Next**.

![screenshot_2026-04-15_111858.png](/attachments/screenshot_2026-04-15_111858.png)

9. After installation you should see and **Successful** on the screen.

![screenshot_2026-04-15_111954.png](/attachments/screenshot_2026-04-15_111954.png)

## Post-Installation

1. Once installed, open **lmtools** by searching for it in Windows, or navigate to the default installation path:
   `C:\Program Files\Siemens\lmtools.exe`

2. lmtools will be covered in detail later in this document. For now, go to **Server Status → Perform Server Enquiry**, which should display the issued licenses.