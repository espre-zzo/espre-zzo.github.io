---
title: "Ubuntu Server Network Setup - Netplan & SSH Configuration"
date: 2026-02-14 14:00:00 +0900
categories: [Programming, Ubuntu Server]
tags: [ubuntu, network, netplan, ssh, ethernet]
---

This guide covers how to configure network interfaces using **Netplan** on Ubuntu Server. Ubuntu manages networks using YAML-based configuration files located in `/etc/netplan/`.

## Prerequisites

- **Root/Sudo Privileges** required
- Text editor such as `nano` or `vim`

> YAML is sensitive to indentation. **You must use spaces (2 or 4 spaces) and DO NOT use tab.**
{: .prompt-warning }

---

## 1. Check Network Interface

Before configuration, identify the physical network interface name (e.g., `eth0`, `enp3s0`).

> Note: [Ubuntu Network Configuration Official Documentation](https://ubuntu.com/server/docs/network-configuration)
{: .prompt-info }

### List Interfaces

```bash
ip a
```

Output Example:

```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 ...
    inet 127.0.0.1/8 scope host lo
2: enp0s25: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 ...
    inet 10.102.66.200/24 brd 10.102.66.255 scope global dynamic eth0
```

### Netplan Configuration File

Managed via `00-installer-config.yaml` file in the `/etc/netplan/` directory.

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth_lan0:
      dhcp4: true
      match:
        macaddress: 00:11:22:33:44:55
      set-name: eth_lan0
```

---

### IP Address Configuration

> **Sudo privileges** are required to the network settings.
{: .prompt-warning }

#### DHCP Configuration (Automatic IP Assignment)

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp3s0:
      dhcp4: true
```

#### Static IP Configuration (Fixed IP)

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: no
      addresses:
        - 10.x.x.x/24
      # 'gateway4' is deprecated. Use 'routes' instead:
      routes:
        - to: default
          via: 10.x.x.x
      nameservers:
        search: [mydomain, otherdomain]
        addresses: [10.x.x.x, 1.1.1.1]
```

#### Apply Changes

```bash
sudo netplan apply
```

> You **MUST** execute `netplan apply` after modifying the configuration file for changes to take effect.
{: .prompt-danger }

---

## 2. SSH Service Configuration

SSH (Secure Shell) is a protocol for safely transmitting commands over an unsecured network. SSH uses encryption to authenticate and encrypt connections between devices.

> Tip: [Cloudflare - SSH란?](https://www.cloudflare.com/ko-kr/learning/access-management/what-is-ssh/)
{: .prompt-info }

### Edit SSH Cofiguration File

```bash
sudo nano /etc/ssh/sshd_config
```

### Change Port

You can change the default port (22) to a different port:

```
Port {####}
```

### Apply Changes

```bash
sudo systemctl restart ssh
```
