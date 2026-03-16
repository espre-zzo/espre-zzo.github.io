---
title: "Mounting Google Shared Drives on Ubuntu Server with rclone"
date: 2026-03-14
categories: [Programming, Ubuntu Server]
tags: [ubuntu, rclone, google-drive, shared-drive]
---

## Overview

 Previous method only covered "My Drive" which does not show me **Google Shared Drives** (formerly Team Drives). This is a follow-up to [Mounting Google Drive on Ubuntu Server with rclone]({% post_url 2026-03-13-google-drive-rclone-mount %}).

## Prerequisites

- A working `rclone` setup with Google Drive already configured (see the previous post)
- The OAuth token from the existing `gdrive` remote in `~/.config/rclone/rclone.conf`

## 1. Find Shared Drive IDs

To list all Shared Drives available to your account, use the Google Drive API:

```bash
python3 -c "
import json, urllib.request

with open('$HOME/.config/rclone/rclone.conf') as f:
    for line in f:
        if line.startswith('token'):
            token = json.loads(line.split('=', 1)[1].strip())
            break

req = urllib.request.Request(
    'https://www.googleapis.com/drive/v3/drives?pageSize=100',
    headers={'Authorization': f'Bearer {token[\"access_token\"]}'}
)
resp = urllib.request.urlopen(req)
for d in json.loads(resp.read()).get('drives', []):
    print(f'  ID: {d[\"id\"]}  Name: {d[\"name\"]}')
"
```

> If the access token has expired, run `rclone lsd gdrive:` first to refresh it, then try again.
{: .prompt-tip }

## 2. Add Shared Drive Remotes to rclone Config

Edit `~/.config/rclone/rclone.conf` and add a new section for each Shared Drive. The key difference from a personal drive config is the `team_drive` field:

```ini
[gdrive-shared-example]
type = drive
scope = drive
token = <SAME_TOKEN_AS_YOUR_GDRIVE_REMOTE>
team_drive = <SHARED_DRIVE_ID>
```

For example, to add two Shared Drives:

```ini
[gdrive-shared-a]
type = drive
scope = drive
token = {"access_token":"...","token_type":"Bearer","refresh_token":"...","expiry":"..."}
team_drive = <SHARED_DRIVE_ID_A>

[gdrive-shared-b]
type = drive
scope = drive
token = {"access_token":"...","token_type":"Bearer","refresh_token":"...","expiry":"..."}
team_drive = <SHARED_DRIVE_ID_B>
```

> Use the same `token` value from your existing `[gdrive]` remote. Each Shared Drive only needs a unique `team_drive` ID.
{: .prompt-info }

### Verify the Connection

```bash
rclone lsd gdrive-shared-a: --max-depth 1
rclone lsd gdrive-shared-b: --max-depth 1
```

## 3. Create Mount Points

```bash
mkdir -p ~/google-drive-shared/shared-a
mkdir -p ~/google-drive-shared/shared-b
```

Test with a manual mount:

```bash
rclone mount gdrive-shared-a: ~/google-drive-shared/shared-a --vfs-cache-mode writes --daemon
ls ~/google-drive-shared/shared-a
```

## 4. Auto-Mount with systemd

Create a systemd user service for each Shared Drive.

### Create Service Files

`~/.config/systemd/user/rclone-gdrive-shared-a.service`:

```ini
[Unit]
Description=Mount Google Shared Drive (shared-a) with rclone
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
ExecStart=/usr/bin/rclone mount gdrive-shared-a: %h/google-drive-shared/shared-a --vfs-cache-mode writes
ExecStop=/bin/fusermount -u %h/google-drive-shared/shared-a
Restart=on-failure
RestartSec=5

[Install]
WantedBy=default.target
```

`~/.config/systemd/user/rclone-gdrive-shared-b.service`:

```ini
[Unit]
Description=Mount Google Shared Drive (shared-b) with rclone
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
ExecStart=/usr/bin/rclone mount gdrive-shared-b: %h/google-drive-shared/shared-b --vfs-cache-mode writes
ExecStop=/bin/fusermount -u %h/google-drive-shared/shared-b
Restart=on-failure
RestartSec=5

[Install]
WantedBy=default.target
```

### Enable and Start

```bash
systemctl --user daemon-reload
systemctl --user enable --now rclone-gdrive-shared-a.service
systemctl --user enable --now rclone-gdrive-shared-b.service
```

### Verify

```bash
systemctl --user status rclone-gdrive-shared-a
systemctl --user status rclone-gdrive-shared-b
ls ~/google-drive-shared/shared-a
ls ~/google-drive-shared/shared-b
```

## Final Mount Structure

| Drive | Mount Path | Service |
|-------|-----------|---------|
| My Drive | `~/google-drive/` | `rclone-gdrive` |
| Shared Drive A | `~/google-drive-shared/shared-a/` | `rclone-gdrive-shared-a` |
| Shared Drive B | `~/google-drive-shared/shared-b/` | `rclone-gdrive-shared-b` |

## Useful Commands

| Action | Command |
|--------|---------|
| Check all statuses | `systemctl --user status rclone-gdrive*` |
| Stop a mount | `systemctl --user stop rclone-gdrive-shared-a` |
| View logs | `journalctl --user -u rclone-gdrive-shared-a` |
| List available Shared Drives | See the Python script in Step 1 |
