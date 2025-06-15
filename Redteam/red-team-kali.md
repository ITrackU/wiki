---
title: Kali Linux
description: Installation of the most used os for Pentesting
published: true
date: 2025-04-17T12:43:15.807Z
tags: osint, kali, pentest
editor: markdown
dateCreated: 2025-04-17T12:43:13.424Z
---

# 🐍 Kali Linux Installation and Secure Remote Access Guide

## 🧰 Prerequisites

- A dedicated local machine (or VM) with at least:
  - 2+ GHz dual-core processor
  - 4GB+ RAM
  - 20GB+ free disk space
- USB drive (8GB+) to create a bootable installer
- Reliable internet connection
- Router/modem access for port forwarding
- Optional: Public IP or Dynamic DNS service (e.g., DuckDNS)

---

## 🔥 Step 1: Download Kali Linux ISO

1. Go to the [official Kali website](https://www.kali.org/get-kali/#kali-installer-images).
2. Download the latest **Installer ISO** (choose the right architecture: `amd64` for most systems).
3. Verify the download using SHA256 or GPG signature (for integrity and authenticity):
    ```bash
    sha256sum kali-linux-*.iso
    ```

---

## 🧪 Step 2: Create a Bootable USB

Use a tool like **Rufus** (Windows), **balenaEtcher**, or `dd` (Linux/macOS):

```bash
sudo dd if=kali-linux-*.iso of=/dev/sdX bs=4M status=progress
```

> Replace `/dev/sdX` with your USB device (⚠️ Be careful — this will erase it).

Make sure to verify your USB device using:
```bash
lsblk
```
or
```bash
sudo fdisk -l
```

---

## 💻 Step 3: Install Kali Linux

1. Boot from USB (change BIOS boot order if needed).
2. Select **Graphical Install** or **Text Install**.
3. Follow the prompts:
   - Choose keyboard layout
   - Set hostname (e.g., `kali-local`)
   - Configure users and password
   - Partition disk (use entire disk if unsure)
   - Install GRUB bootloader
4. Reboot and remove USB.

---

## 🌐 Step 4: Configure Networking

### Check current IP:
```bash
ip a
```

### Set static IP (optional but recommended for remote access):

Edit `/etc/network/interfaces` or use `netplan` depending on your setup. Example for `interfaces`:

```ini
auto eth0
iface eth0 inet static
  address 192.168.1.100
  netmask 255.255.255.0
  gateway 192.168.1.1
  dns-nameservers 1.1.1.1 8.8.8.8
```

Restart networking:
```bash
sudo systemctl restart networking
```

---

## 🔐 Step 5: Enable SSH Access

```bash
sudo apt update
sudo apt install -y openssh-server
sudo systemctl enable ssh
sudo systemctl start ssh
```

### Check SSH status:
```bash
sudo systemctl status ssh
```

### Check local access:
```bash
ssh user@localhost
```

---

## 🌍 Step 6: Remote Access Setup

### 1. **Router Port Forwarding**

- Forward external port `2222` → internal `22` on `192.168.1.100` (your Kali box)
- Each router interface differs (refer to router manual or web UI)

### 2. **Public IP or Dynamic DNS**

- Get your public IP: [https://ifconfig.me](https://ifconfig.me)
- Set up dynamic DNS (e.g., DuckDNS, No-IP) if IP is dynamic

---

## 🛡️ Step 7: Secure Your Kali Box

### Basic hardening:

```bash
# Change default SSH port (optional)
sudo nano /etc/ssh/sshd_config
# Port 2222

# Disable root login via SSH
PermitRootLogin no

# Use key-based authentication (recommended)
ssh-keygen -t ed25519
ssh-copy-id user@your-public-ip -p 2222

# Restart SSH
sudo systemctl restart ssh
```

### Enable UFW Firewall:

```bash
sudo apt install ufw
sudo ufw allow 2222/tcp
sudo ufw enable
```

### Fail2ban to prevent brute-force attacks:

```bash
sudo apt install fail2ban
sudo systemctl enable fail2ban
```

---

## 🧪 Test External Access

From another device or external network:

```bash
ssh user@your-public-ip -p 2222
```

---

## 🧼 Maintenance and Monitoring

- Regularly update:
  ```bash
  sudo apt update && sudo apt upgrade
  ```
- Check login attempts:
  ```bash
  sudo journalctl -u ssh
  sudo cat /var/log/auth.log
  ```
- Reboot periodically and audit users/services.

---

## 🏁 Final Notes

- Always lock down unnecessary services.
- Avoid using root unless absolutely necessary.
- Use VPN for added security in production environments.
- Use Kali responsibly — it’s a security tool, not a toy.

---

## 📚 References

- [Kali Docs](https://docs.kali.org/)
- [OpenSSH Manual](https://man.openbsd.org/sshd)
- [Fail2Ban GitHub](https://github.com/fail2ban/fail2ban)

---

_This guide is for educational and ethical use only. Always obtain permission before scanning or testing any systems you don’t own._
