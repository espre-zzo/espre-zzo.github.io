---
title: "Ubuntu Server Management - Time Settings & User Management"
date: 2026-02-15 14:00:00 +0900
categories: [Programming, Ubuntu Server]
tags: [ubuntu, management]
---

This document introduces essential commands for time management and user management on Ubuntu Server.

---

## 1. Time Management

### Check Current Status

```bash
timedatectl
```

### Change Timezone

Example: To change to Seoul(KST)

```bash
# Search for Seoul in the available timezone list
timedatectl list-timezones | grep Seoul

# Set timezone
sudo timedatectl set-timezone Asia/Seoul
```

### NTP Synchronization (Recommended)

Ubuntu Server uses `systemd-timesyncd` by default. However, for servers requiring high precision (e.g., databases, clusters), dedicated services like **Chrony** or **NTPd** are preferred.

> **Important:** Do not run multiple time services simultaneously (e.g., `timesyncd` + `chrony`). This will cause conflicts.

Automatically synchronize time with internet servers(systemd-timesyncd):

```bash
# Enable default synchronization
sudo timedatectl set-ntp on

# Check status
systemctl status systemd-timesyncd
```

### Manual Time Setting

> You must **disable NTP** before manually setting the time.
{: .prompt-warning }

```bash
# Disable NTP
sudo timedatectl set-ntp off

# Set time (Format: YYYY-MM-DD HH:MM:SS)
sudo date -s "2025-12-17 14:30:00"
# Or
sudo timedatectl set-time "2025-12-17 14:30:00"

# Synchronize hardware clock
sudo hwclock --systohc
```

---

## 2. User Management

### Add/Remove Users

> **Sudo privileges** are required to modify users.
{: .prompt-warning }

```bash
# Add user
sudo adduser {USER NAME}

# Delete user
sudo userdel {USER NAME}

# Delete user + remove home directory (Recommended)
sudo userdel -r {USER NAME}
```

### Change Password

```bash
sudo passwd {USER NAME}
```

### Grant sudo Privileges

```bash
sudo usermod -aG sudo {USER NAME}
```
