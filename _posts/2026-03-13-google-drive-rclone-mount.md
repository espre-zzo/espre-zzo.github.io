---
title: "Mounting Google Drive on Ubuntu Server with rclone"
date: 2026-03-13
categories: [Programming, Ubuntu Server]
tags: [ubuntu, rclone, google-drive]
---

## Overview

I found it quite cumbersome to share files generated on the server. So, this post covers how to mount Google Drive on a headless Ubuntu server using `rclone`. Since the server has no GUI browser, I used **remote authorization** from a local machine to obtain the OAuth token.

## Prerequisites

- Ubuntu server with `rclone` installed
- A local machine with a **web browser** and `rclone` installed
- A Google account

To install `rclone` on the server:

```bash
sudo apt install rclone
```

For the local machine, visit [rclone.org/downloads](https://rclone.org/downloads/) or use a package manager (e.g., `brew install rclone` on macOS).

## 1. Obtain OAuth Token on Local Machine

On your **local machine** (the one with a browser), run:

```bash
rclone authorize "drive"
```

This will:
1. Open a browser window for Google login (It takes some time.)
2. Ask you to grant access to your Google Drive
3. Print a JSON token in the terminal

Copy the entire JSON block that appears between `Paste the following into your remote machine --->` and `<---End paste`.

## 2. Configure rclone on the Server

Create the rclone config file on the server:

```bash
mkdir -p ~/.config/rclone
```

Edit `~/.config/rclone/rclone.conf` and add the following:

```ini
[gdrive]
type = drive
scope = drive
token = <PASTE_YOUR_TOKEN_JSON_HERE>
```

> Replace `<PASTE_YOUR_TOKEN_JSON_HERE>` with the JSON token obtained in the previous step.
{: .prompt-info }

### Verify the Connection

```bash
rclone lsd gdrive: --max-depth 1
```

If configured correctly, this will list the top-level folders in your Google Drive.

## 3. Mount Google Drive

Create a mount point and test the mount:

```bash
mkdir -p ~/google-drive
rclone mount gdrive: ~/google-drive --vfs-cache-mode writes --daemon
```

Verify with `ls ~/google-drive` to see your Drive contents.

## 4. Auto-Mount with systemd

To persist the mount across reboots, create a systemd user service.

### Create the Service File

```bash
mkdir -p ~/.config/systemd/user
```

Create `~/.config/systemd/user/rclone-gdrive.service`:

```ini
[Unit]
Description=Mount Google Drive with rclone
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
ExecStart=/usr/bin/rclone mount gdrive: %h/google-drive --vfs-cache-mode writes
ExecStop=/bin/fusermount -u %h/google-drive
Restart=on-failure
RestartSec=5

[Install]
WantedBy=default.target
```

> `%h` is a systemd specifier that expands to the user's home directory.
{: .prompt-tip }

### Enable and Start the Service

If you previously mounted with `--daemon`, unmount it first:

```bash
fusermount -u ~/google-drive
```

Then reload systemd, enable, and start the service:

```bash
systemctl --user daemon-reload
systemctl --user enable --now rclone-gdrive.service
```

To ensure the service runs even when you are not logged in:

```bash
sudo loginctl enable-linger $USER
```

### Verify

```bash
systemctl --user status rclone-gdrive
ls ~/google-drive
```

## Commands

| Action | Command |
|--------|---------|
| Check status | `systemctl --user status rclone-gdrive` |
| Stop mount | `systemctl --user stop rclone-gdrive` |
| Start mount | `systemctl --user start rclone-gdrive` |
| Disable auto-mount | `systemctl --user disable rclone-gdrive` |
| View logs | `journalctl --user -u rclone-gdrive` |
