---
title: Unix User Management
description: Commands and knowledge about Unix Users management
published: true
date: 2025-04-16T14:54:05.062Z
tags: bash, unix, users, shell, rights
editor: markdown
dateCreated: 2025-04-16T14:53:57.971Z
---

# 🧑‍💻 Linux User Management Wiki

## 📚 Table of Contents

1. [Overview](#overview)
2. [User Account Types](#user-account-types)
3. [Creating Users](#creating-users)
4. [Managing Passwords](#managing-passwords)
5. [Modifying Users](#modifying-users)
6. [Deleting Users](#deleting-users)
7. [Group Management](#group-management)
8. [User Home Directories](#user-home-directories)
9. [User Permissions](#user-permissions)
10. [Account Policies & Expiry](#account-policies--expiry)
11. [User Info Files](#important-system-files)
12. [Shell Management](#shell-management)
13. [Socket Management](#socket-management)
14. [Best Practices](#best-practices)
15. [Common Commands Cheat Sheet](#common-commands-cheat-sheet)

---

## 💾 Overview

Linux user management involves creating, modifying, and removing user accounts and groups. Each user has a unique User ID (UID), home directory, default shell, and optional group memberships.

---

## 👤 User Account Types

| Type         | Description                          |
|--------------|--------------------------------------|
| **Root**     | Superuser with full system access    |
| **System**   | Used by services/processes           |
| **Regular**  | Normal human users                   |
| **Pseudo Users** | No login access, used by daemons  |

---

## ➕ Creating Users

### `useradd` (Basic)

```bash
sudo useradd username
```

### With Options

```bash
sudo useradd -m -s /bin/bash -G sudo username
```

| Option | Description                  |
|--------|------------------------------|
| `-m`   | Create home directory        |
| `-s`   | Set default shell            |
| `-G`   | Add to supplementary groups  |

### Example

```bash
sudo useradd -m -s /bin/zsh -G developers alice
```

---

## 🔐 Managing Passwords

### Set or Change Password

```bash
sudo passwd username
```

### Force Password Change at Next Login

```bash
sudo chage -d 0 username
```

---

## ✏️ Modifying Users

### Change Username

```bash
sudo usermod -l newname oldname
```

### Change Home Directory

```bash
sudo usermod -d /new/home -m username
```

### Add User to Group

```bash
sudo usermod -aG groupname username
```

---

## ❌ Deleting Users

### Remove User (Keep Home)

```bash
sudo userdel username
```

### Remove User and Home Directory

```bash
sudo userdel -r username
```

---

## 👥 Group Management

### Create a Group

```bash
sudo groupadd groupname
```

### Delete a Group

```bash
sudo groupdel groupname
```

### Add User to Group

```bash
sudo gpasswd -a username groupname
```

### Remove User from Group

```bash
sudo gpasswd -d username groupname
```

### List Groups

```bash
groups username
```

---

## 🏠 User Home Directories

- Default location: `/home/username`
- Set custom home dir on creation:

```bash
sudo useradd -m -d /custom/home username
```

- Copy default skeleton files from `/etc/skel/`

---

## 🛡️ User Permissions

- Controlled via **file ownership** and **permissions**.
- Change ownership:

```bash
sudo chown username:groupname file
```

- Change permissions:

```bash
chmod 755 file
```

---

## 📅 Account Policies & Expiry

### View Account Info

```bash
chage -l username
```

### Set Expiry Date

```bash
sudo chage -E YYYY-MM-DD username
```

### Lock/Unlock Account

```bash
sudo usermod -L username   # Lock
sudo usermod -U username   # Unlock
```

---

## 📂 Important System Files

| File                | Purpose                               |
|---------------------|----------------------------------------|
| `/etc/passwd`       | User account info                     |
| `/etc/shadow`       | Encrypted passwords & policies        |
| `/etc/group`        | Group definitions                     |
| `/etc/login.defs`   | Default settings for user accounts    |
| `/etc/skel/`        | Skeleton directory for new users      |

---

## 🔣 Shell Management

- View available shells:

```bash
cat /etc/shells
```

- Change a user's default shell:

```bash
sudo chsh -s /bin/zsh username
```

- Set shell during user creation:

```bash
sudo useradd -s /bin/bash username
```

- Common Shells:
  - `/bin/bash`
  - `/bin/zsh`
  - `/bin/sh`
  - `/usr/bin/fish`

---

## 📡 Socket Management

- **List all active sockets:**

```bash
ss -tuln
```

- **Detailed socket info (process + ports):**

```bash
sudo ss -tulnp
```

- **Monitor socket activity:**

```bash
watch ss -tuln
```

- **Use `netstat` as alternative (legacy):**

```bash
sudo netstat -tulnp
```

- **View UNIX domain sockets:**

```bash
ss -x
```

---

## ✅ Best Practices

- Disable root login via SSH
- Use `sudo` instead of direct root
- Create system users for services
- Lock unused accounts
- Regularly audit users and groups

---

## ⚡ Common Commands Cheat Sheet

| Task                         | Command                                   |
|------------------------------|-------------------------------------------|
| Create user                  | `sudo useradd -m username`                |
| Delete user + home           | `sudo userdel -r username`                |
| Set password                 | `sudo passwd username`                    |
| Add to group                 | `sudo usermod -aG group user`             |
| List groups for user         | `groups username`                         |
| Show account aging           | `chage -l username`                       |
| Lock user account            | `sudo usermod -L username`                |
| Change default shell         | `chsh -s /bin/bash username`              |
| List open sockets            | `ss -tulnp`                               |

---

