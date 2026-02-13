---
title: "Ubuntu Server Installation Guide - Creating Boot USB & BIOS Setup"
date: 2026-02-13 14:00:00 +0900
categories: [Programming, Ubuntu Server]
tags: [ubuntu, installation, rufus]
---

This document covers the preliminary steps for installing Ubuntu Server on a physical machine, including creating a bootable USB drive and configuring BIOS/UEFI settings.

## Prerequisites

- USB Flash Drive (Minimum 4GB, data will be erased)
- **Ubuntu Server ISO** file (Download from [Ubuntu Official Site](https://ubuntu.com/download/server))
- Bootable drive creation software (e.g., [Rufus](https://rufus.ie/) for Windows)

---

## 1. Creating a Bootable Installation Disk

### Step 1: Prepare Software

1. Download and install **Rufus**.
2. Connect the USB drive to the computer.

> It is recommended to use the USB ports on the back of the motherboard.
{: .prompt-tip }

### Step 2: Flashing the ISO

1. Run Rufus.
2. **Select Image:** Choose the downloaded `ubuntu-server-xx.xx.iso` file.
3. **Select Drive:** Select the USB flash drive as the target.
4. Click the **Start** (or **Flash!**) button to begin the process.

> The USB drive will be formatted during this process. Be sure to backup any important data!
{: .prompt-danger }

---

## 2. BIOS/UEFI Boot Configuration

Once the boot USB is ready, you must configure the system to boot from the USB instead of the internal hard drive.

### Step 1: Enter BIOS/UEFI Settings

1. Insert the boot USB into the target server/PC.
2. Turn on the power.
3. Repeatedly press the **BIOS Key** to enter the setup screen.

| Common BIOS Keys |
|:---:|
| `F2`, `Del`, `F10`, `F12` |

> You can usually check the correct key on the motherboard manufacturer's logo screen.
{: .prompt-info }

### Step 2: Change Boot Order

1. Use the arrow keys to navigate to the **Boot** tab (or **Boot Priority** settings).
2. Find the **USB Flash Drive** in the list.
3. Move the USB drive to the **#1 position** (top of the list).
   - Usually done using `+` / `-` keys or `F5` / `F6` keys.
4. Save and exit (usually `F10`).