---
title: "Connecting Synology NAS to Ubuntu Server via NFS"
date: 2026-02-16 14:00:00 +0900
categories: [Programming, Ubuntu Server]
tags: [ubuntu, nas, nfs]
---

This guide outlines how to mount a shared folder from a Synology NAS to an Ubuntu Server using the NFS (Network File System) protocol.

## Prerequisites

- **Synology DSM** Administrator access
- **Sudo privileges** on Ubuntu Server
- IP Address of the NAS (e.g., `xxx.xxx.xxx.xxx`)
- Volume path on the NAS (e.g., `/volume1/Data`)

---

## 1. Synology NAS Configuration

Before configuring the Ubuntu server, you must enable NFS on the NAS.

### 1.1. Enable NFS Service

1. Log in to DSM.
2. Go to **Control Panel** > **File Services** > **NFS** tab.
3. Check **Enable NFS service**.
4. Click **Apply**.

### 1.2. Shared Folder Permissions

1. Go to **Control Panel** > **Shared Folder**.
2. Select the target folder (e.g., `Data`) and click **Edit**.
3. Go to the **NFS Permissions** tab and click **Create**. 
| Item | Value |
|------|--------|
| **Hostname/IP** | IP Address of the Ubuntu Server |
| **Privilege** | Read/Write |
| **Squash** | Map all users to admin |
| **Security** | `sys` |

> Note the "Mount path" displayed at the bottom of the edit window (e.g., `/volume1/Data`). Linux is case-sensitive!
{: .prompt-tip }

---

## 2. Ubuntu Client Configuration

### 2.1. Install NFS Client

Install the necessary packages to handle the NFS filesystem.

```bash
sudo apt update
sudo apt install nfs-common -y
```

### 2.2. Create Mount Point

Create a local directory where the NAS folder will be connected.

```bash
sudo mkdir -p /mnt/nas_data
```

### 2.3. Manual Mount

```bash
sudo mount -t nfs xxx.xxx.xxx.xx:/volume1/Data /mnt/nas_data
```

### 2.4. Automatic Mount Configuration (/etc/fstab)

Configure the system to automatically mount the folder upon restart.

```bash
sudo nano /etc/fstab
```

Add the following line to the bottom of the file:

```
xxx.xxx.xxx.xx:/volume1/Data /mnt/nas_data nfs defaults,nofail,_netdev 0 0
```

- `defaults`: Uses default mount settings (rw, suid, dev, exec, auto, nouser, and async).
- `nofail`: The boot process will continue even if this mount fails (essential for headless servers).
- `_netdev`: Tells the system that this mount requires network access, so it waits until the network is up


> After configuration, run `sudo mount -a` to ensure it mounts without errors.
{: .prompt-tip }
