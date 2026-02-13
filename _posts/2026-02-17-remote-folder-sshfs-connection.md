---
title: "Mounting Remote Data Center Folder via SSHFS"
date: 2026-02-17 14:00:00 +0900
categories: [Programming, Ubuntu Server]
tags: [ubuntu, sshfs, ssh, mount, remote, fstab]
---

This guide outlines how to mount a specific folder from a remote data center (Private IP) to a local workstation (Public IP) using `SSHFS` (SSH File System). It covers using a custom SSH port, passwordless login, and automatic mounting at boot.

## Prerequisites

- **Local Workstation**: Ubuntu/Linux (Public IP)
- **Remote Data Folder**: Ubuntu/Linux, SSH accessible (Private IP)
- **Network**: VPN connection or Allowlist configuration required
- **SSH Port**: Custom port usage
- **Sudo privileges** on Ubuntu Server

---

## 1. Installation and Preparation

### 1.1. Install SSHFS

Install the `sshfs` package on the local workstation.

```bash
sudo apt update
sudo apt install sshfs -y
```

### 1.2. Create Mount Point (in Local Workstation)

Create the local directory where the remote folder will be connected.

```bash
# Create directory
mkdir -p /home/user/raw_data/

# Set ownership to local user (Important for permissions)
chmod 700 /home/user/raw_data/
```

---

## 2. SSH Key Setup (Passwordless Login)

Generate an SSH Key pair and transfer it to the server to enable **automatic mounting** without entering a password every time.

### 2.1. Generate SSH Key

```bash
ssh-keygen -t rsa
# Press Enter at all prompts (Do not set a passphrase for auto-mount)
```

### 2.2. Copy Public Key to Remote Server

Use the `-p` option if using a custom port.

```bash
ssh-copy-id -p #### user@***.***.***.**
```

### 2.3. Test Connection

Verify that you can log in without a password.

```bash
ssh -p #### user@***.***.***.**
```

---

## 3. Mount Remote Folder

### 3.1. Manual Mount

Perform a manual mount first to verify that `idmap` and port options work correctly.

```bash
sshfs -p #### -o idmap=user user@***.***.***.**:/data01/local_data/ /home/user/raw_data/
```

| Option | Description |
|------|------|
| `-p ####` | Specify custom SSH port |
| `-o idmap=user` | Maps remote file ownership (UID) to the local user. Solves issues where files appear as `1001` or `nobody`. |

### 3.2. Unmount

```bash
fusermount -u /home/user/raw_data/
```

---

## 4. Automatic Mount at Boot (/etc/fstab)

Edit the filesystem table to automatically mount the folder when the workstation restarts.

### 4.1. Open fstab Configuration File

```bash
sudo nano /etc/fstab
```

### 4.2. Add Configuration

Add the following line:

```
user@x.x.x.x:/data01/local_data/ /home/user/raw_data/ fuse.sshfs port=####,idmap=user,_netdev,IdentityFile=/home/user/.ssh/id_rsa 0 0
```

| Option | Description |
|------|------|
| `port=####` | Connect via custom port |
| `idmap=user` | Solves file ownership/permission display issues |
| `_netdev` | Attempts mount only after network is online (Prevents boot errors) |
| `IdentityFile=...` | Specifies path to SSH private key for authentication |

### 4.3. Verify Configuration

Check for errors without rebooting:

```bash
sudo mount -a
```

---

## 5. Security and Access Control

By default, FUSE filesystems are accessible only by the user who mounted them.

### 5.1. Restrict Access

To ensure only the **connecting user** can access the data:

1. **Do not use** `allow_other` in the `sshfs` command or `/etc/fstab`.
2. After unmounting, set the mount point permissions to `700`.

### 5.2. Allow Access (Optional)

If you need to share with other users on the workstation:

1. Uncomment `user_allow_other` in `/etc/fuse.conf`.
2. Add `,allow_other` to the mount command or `/etc/fstab` options.

---

## 6. Troubleshooting (according to my experience)

| Symptom | Cause | Solution |
|:---|:---|:---|
| `read: Connection reset by peer` | Wrong port or network issue (VPN disconnected, Firewall, etc.) | Specify with `-p` option, check VPN connection |
| File showing `1001` or `nobody` | UID Mismatch (Server UID differs from Local UID) | Add option `-o idmap=user` |
| `option allow_other only allowed if...` | Non-root users restricted from using `allow_other` | Uncomment `user_allow_other` in `/etc/fuse.conf` |
| `target is busy` | Terminal or file explorer is currently inside the mounted folder | `cd ~`to move out, the retry `fusermount -u ...` |
| Bricked terminal/Unresponsive | Server connection lost while mounted | `sudo umount -l /home/user/raw_data/` (lazy unmount) |
