---
title: "RStudio Server Installation & Management Guide (Ubuntu 20.04 / 24.04)"
date: 2026-02-18 14:00:00 +0900
categories: [Programming, Ubuntu Server]
tags: [ubuntu, rstudio, r, rig, server]
---

This guide outlines the procedure for building an RStudio Server environment on Ubuntu. It covers pinning R versions using `rig`, managing system dependencies, and configuring session timeouts.

---

## 1. System Preparation

Before installing R, update system repositories and install essential build tools.

### 1.1. System Update & Dependency Installation

> It is strongly recommended to install system-level development libraries **first** to prevent R package compilation errors (especially for spatial/graphic packages).
{: .prompt-tip }

```bash
# Update repositories
sudo apt update && sudo apt upgrade -y

# Install basic build tools
sudo apt install -y build-essential libcurl4-gnutls-dev libxml2-dev \
  libssl-dev curl gdebi-core default-jdk

# Install GIS, graphics, and font libraries (Essential for sf, terra, tidyverse, etc.)
sudo apt install -y libgdal-dev libproj-dev libgeos-dev libudunits2-dev \
  libnode-dev libcairo2-dev libnetcdf-dev libharfbuzz-dev libfribidi-dev \
  libpng-dev libjpeg-dev libtiff-dev
```

---

## 2. R Installation (Pinning Version with rig)

**Use `rig` (The R Installation Manager)** to install specific R versions and prevent overwrites caused by `apt upgrade`.

### 2.1. Install rig

```bash
curl -Ls https://github.com/r-lib/rig/releases/download/latest/rig-linux-amd64.tar.gz \
  | sudo tar xz -C /usr/local
```

### 2.2. Install specific R version

```bash
# R 4.3.3 Installation (for example)
sudo rig install 4.3.3

# Set as system default
sudo rig default 4.3.3

# Configure Java (Resolve rJava issue)
sudo R CMD javareconf
```

### 2.3. Prevent System R Installation (Optional, Recommended)

Prevent other sudo users from accidentally installing the `apt` version of R.

```bash
sudo apt-mark hold r-base r-base-core r-base-dev r-recommended
```

> Since R installed via `rig` and R installed via `apt` can conflict, holding the apt packages is recommended.
{: .prompt-warning }

---

## 3. RStudio Server Installation

### 3.1. Download and Installation

Note: It is crucial to install the correct build for your OS release. Installing a version designed for Ubuntu 22.04/24.04 on Ubuntu 20.04 will cause libssl dependency errors.

**Ubuntu 24.04 (Noble) / 22.04 (Jammy):**Uses standard official release (OpenSSL 3.0 support).

```bash
sudo apt-get install gdebi-core
wget https://download2.rstudio.org/server/jammy/amd64/rstudio-server-2024.04.2-764-amd64.deb
sudo gdebi -n rstudio-server-*-amd64.deb
```

**Ubuntu 20.04 (Focal):**Used the specific build 2024.04.3+777 (Daily Build) to get most recent update and ensure OpenSSL 1.1 compatibility.

```bash
sudo apt-get install gdebi-core
# Download the specific build for Focal (Ubuntu 20.04)
wget https://s3.amazonaws.com/rstudio-ide-build/server/focal/amd64/rstudio-server-2024.04.3-777-amd64.deb
sudo gdebi -n rstudio-server-2024.04.3-777-amd64.deb
```

### 3.2. Verification

```bash
# Verify the service status
sudo systemctl status rstudio-server

# Verify the installed version
rstudio-server version
```

---

## 4. Configuration

### 4.1. Pinning a Specific R Version

Force RStudio Server to use a specific R version installed via `rig` (e.g., `/opt/R/...`).

```bash
sudo nano /etc/rstudio/rserver.conf
```

Add the following line:

```ini
# Path to the specific R version
rsession-which-r=/opt/R/4.3.3/bin/R
```

### 4.2. Port Configuration (Optional)

If you need to use a port other than the default `8787` (e.g., due to firewall restrictions):

```bash
sudo nano /etc/rstudio/rserver.conf
```

Add or modify:

```ini, TOML
www-port=xxxx
```

### 4.3. Disabling Session Timeouts

Configure RStudio to prevent automatic session suspension or logout.

**Prevent Session Suspension (Keep R processes alive):**

```bash
sudo nano /etc/rstudio/rsession.conf
```

```ini,TOML
# 0 = Unlimited (No timeout)
session-timeout-minutes=0
```

**Prevent Authentication Timeout (Keep users logged in):**

```bash
sudo nano /etc/rstudio/rserver.conf
```

```ini
auth-timeout-minutes=0
```

### 4.4. Applying Changes

Restart the server to apply the configuration changes.

```bash
sudo rstudio-server restart
```
