---
title: Unix Hardening
description: Configurations and Commands to make your linux more secure
published: true
date: 2025-04-17T08:17:59.533Z
tags: security, linux, unix, rhel, ubuntu, debian
editor: markdown
dateCreated: 2025-04-17T08:17:55.455Z
---

# Linux Security Hardening Guide

This comprehensive guide covers best practices and configurations for hardening Linux systems against security threats and vulnerabilities.

## Table of Contents

1. [Initial System Setup](#initial-system-setup)
2. [User Account Management](#user-account-management)
3. [Secure Authentication](#secure-authentication)
4. [File System Security](#file-system-security)
5. [Network Security](#network-security)
6. [Service Hardening](#service-hardening)
7. [Kernel Hardening](#kernel-hardening)
8. [Logging and Monitoring](#logging-and-monitoring)
9. [Regular Maintenance](#regular-maintenance)
10. [Security Tools](#security-tools)
11. [Compliance and Auditing](#compliance-and-auditing)

## Initial System Setup

### Secure Installation

```bash
# Use secure installation settings
# - Enable disk encryption (LUKS)
# - Create separate partitions for /home, /var, /tmp
# - Minimal installation (only install what you need)
```

### Software Updates

```bash
# Debian/Ubuntu systems
apt update && apt upgrade -y

# Red Hat/CentOS systems
dnf update -y
# or
yum update -y

# Configure automatic security updates
# For Debian/Ubuntu
apt install unattended-upgrades
dpkg-reconfigure -plow unattended-upgrades

# For Red Hat/CentOS
dnf install dnf-automatic
systemctl enable --now dnf-automatic.timer
```

### Remove Unnecessary Software

```bash
# List installed packages
dpkg -l  # Debian/Ubuntu
rpm -qa  # Red Hat/CentOS

# Remove unnecessary packages
apt purge <package_name>  # Debian/Ubuntu
dnf remove <package_name>  # Red Hat/CentOS
```

### Disable Unused Services

```bash
# List all running services
systemctl list-units --type=service

# Disable unnecessary services
systemctl disable <service_name>
systemctl stop <service_name>

# Common services to consider disabling if not needed:
# - cups (printing)
# - bluetooth
# - avahi-daemon
# - nfs-server
# - rpcbind
```

## User Account Management

### Strong Password Policy

```bash
# Edit password policy in /etc/security/pwquality.conf
# Add or modify these settings:
minlen = 12
minclass = 4
maxrepeat = 3
dcredit = -1
ucredit = -1
lcredit = -1
ocredit = -1
dictcheck = 1
enforcing = 1

# Edit /etc/login.defs for password aging
PASS_MAX_DAYS   90
PASS_MIN_DAYS   1
PASS_WARN_AGE   7

# Apply changes to existing users
chage -M 90 -m 1 -W 7 <username>
```

### Secure User Accounts

```bash
# Find users with UID 0 (should only be root)
awk -F: '$3 == 0 {print $1}' /etc/passwd

# Find users with empty passwords
awk -F: '($2 == "" || $2 == "*" || $2 == "!") {print $1}' /etc/shadow

# Lock unused accounts
passwd -l <username>

# Set root account to expire
passwd -e root

# Set user account expiry
chage -E $(date -d "+90 days" +%Y-%m-%d) <username>
```

### Implement sudo

```bash
# Install sudo if not installed
apt install sudo  # Debian/Ubuntu
dnf install sudo  # Red Hat/CentOS

# Configure sudo with visudo
visudo

# Example configuration:
# Allow specific users to run all commands
username ALL=(ALL) ALL

# Allow specific commands without password
username ALL=(ALL) NOPASSWD: /sbin/reboot, /sbin/shutdown

# Restrict command execution
username ALL=(ALL) !/bin/bash, !/bin/sh, !/bin/su, !/usr/bin/sudo
```

## Secure Authentication

### SSH Hardening

```bash
# Edit /etc/ssh/sshd_config
# Security-focused settings:
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
Protocol 2
IgnoreRhosts yes
HostbasedAuthentication no
PermitEmptyPasswords no
X11Forwarding no
MaxAuthTries 3
ClientAliveInterval 300
ClientAliveCountMax 0
AllowUsers user1 user2
AllowGroups sshusers

# Use strong ciphers and key exchange algorithms
Ciphers chacha20-poly1305@openssh.com,aes256-gcm@openssh.com,aes128-gcm@openssh.com,aes256-ctr,aes192-ctr,aes128-ctr
KexAlgorithms curve25519-sha256@libssh.org,diffie-hellman-group-exchange-sha256
MACs hmac-sha2-512-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512,hmac-sha2-256

# Restart SSH service
systemctl restart sshd
```

### Set Up 2FA (Two-Factor Authentication)

```bash
# Install Google Authenticator PAM module
apt install libpam-google-authenticator  # Debian/Ubuntu
dnf install google-authenticator         # Red Hat/CentOS

# Configure PAM for SSH
# Edit /etc/pam.d/sshd and add:
auth required pam_google_authenticator.so

# Edit /etc/ssh/sshd_config
ChallengeResponseAuthentication yes

# Initialize for each user
google-authenticator

# Restart SSH service
systemctl restart sshd
```

### Configure PAM (Pluggable Authentication Modules)

```bash
# Edit /etc/security/faillock.conf for account lockout
# Default settings:
deny = 5
fail_interval = 900
unlock_time = 600

# For Debian/Ubuntu, edit /etc/pam.d/common-auth and add:
auth required pam_faillock.so preauth silent audit deny=5 unlock_time=600
auth [success=1 default=bad] pam_unix.so
auth [default=die] pam_faillock.so authfail
```

## File System Security

### Secure Mount Options

```bash
# Edit /etc/fstab and add secure mount options
# Example:
UUID=xxxx /home ext4 defaults,nodev,nosuid,noexec 0 2
UUID=xxxx /tmp ext4 defaults,nodev,nosuid,noexec 0 2
UUID=xxxx /var/tmp ext4 defaults,nodev,nosuid,noexec 0 2
```

### Set Up Disk Encryption

```bash
# For new installations, use LUKS encryption during setup

# For existing disks, create encrypted container
cryptsetup luksFormat /dev/sdX
cryptsetup luksOpen /dev/sdX encrypted_volume
mkfs.ext4 /dev/mapper/encrypted_volume
mount /dev/mapper/encrypted_volume /mnt/secure

# Add to /etc/crypttab for auto-mounting
encrypted_volume /dev/sdX none luks
```

### Set Proper File Permissions

```bash
# Find and fix world-writable files
find / -xdev -type f -perm -0002 -exec chmod o-w {} \;

# Find and fix files with no owner
find / -xdev -nouser -o -nogroup -exec chown root:root {} \;

# Set secure permissions for sensitive files
chmod 600 /etc/shadow
chmod 644 /etc/passwd
chmod 644 /etc/group
chmod 600 /etc/gshadow
chmod 600 /boot/grub/grub.cfg
```

### Implement File Integrity Monitoring

```bash
# Install AIDE (Advanced Intrusion Detection Environment)
apt install aide  # Debian/Ubuntu
dnf install aide  # Red Hat/CentOS

# Initialize AIDE database
aide --init

# Move the initialized database to the active database
mv /var/lib/aide/aide.db.new /var/lib/aide/aide.db

# Set up daily checks
echo '0 5 * * * root /usr/bin/aide --check' > /etc/cron.d/aide

# Verify system integrity
aide --check
```

## Network Security

### Configure Firewall

```bash
# UFW (Uncomplicated Firewall) - Debian/Ubuntu
apt install ufw
ufw default deny incoming
ufw default allow outgoing
ufw allow ssh
ufw allow http
ufw enable

# Firewalld - Red Hat/CentOS
dnf install firewalld
systemctl enable --now firewalld
firewall-cmd --set-default-zone=public
firewall-cmd --zone=public --add-service=ssh --permanent
firewall-cmd --zone=public --add-service=http --permanent
firewall-cmd --reload
```

### Configure Kernel Network Parameters

```bash
# Edit /etc/sysctl.conf or create /etc/sysctl.d/99-security.conf

# Network hardening parameters
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.default.send_redirects = 0
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.default.accept_redirects = 0
net.ipv4.conf.all.secure_redirects = 0
net.ipv4.conf.default.secure_redirects = 0
net.ipv4.conf.all.log_martians = 1
net.ipv4.conf.default.log_martians = 1
net.ipv4.icmp_echo_ignore_broadcasts = 1
net.ipv4.icmp_ignore_bogus_error_responses = 1
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.default.rp_filter = 1
net.ipv4.tcp_syncookies = 1
net.ipv4.conf.all.accept_source_route = 0
net.ipv4.conf.default.accept_source_route = 0
net.ipv6.conf.all.accept_redirects = 0
net.ipv6.conf.default.accept_redirects = 0
net.ipv6.conf.all.accept_ra = 0
net.ipv6.conf.default.accept_ra = 0

# Apply changes
sysctl -p /etc/sysctl.d/99-security.conf
```

### Disable Unused Network Protocols

```bash
# Create /etc/modprobe.d/blacklist.conf
# Disable unused network protocols
blacklist dccp
blacklist sctp
blacklist rds
blacklist tipc
blacklist cramfs
blacklist freevxfs
blacklist jffs2
blacklist hfs
blacklist hfsplus
blacklist squashfs
blacklist udf

# Apply changes
update-initramfs -u  # Debian/Ubuntu
dracut -f           # Red Hat/CentOS
```

### Secure Network Services

```bash
# Use netstat to find open ports
netstat -tulpn

# Use ss to find open ports (newer alternative)
ss -tulpn

# Check which services are using these ports
lsof -i :port_number

# Disable or secure unused services
systemctl disable <service_name>
systemctl stop <service_name>
```

## Service Hardening

### Secure Apache/Nginx

```bash
# Apache - Remove server information
# Edit /etc/apache2/conf-enabled/security.conf
ServerTokens Prod
ServerSignature Off
TraceEnable Off

# Nginx - Remove server information
# Edit /etc/nginx/nginx.conf
server_tokens off;

# Configure SSL/TLS
# Use Mozilla SSL Configuration Generator:
# https://ssl-config.mozilla.org/
```

### Secure Database Servers

```bash
# MySQL/MariaDB Hardening
mysql_secure_installation

# PostgreSQL Hardening
# Edit postgresql.conf
listen_addresses = 'localhost'  # Listen only on localhost
password_encryption = scram-sha-256  # Use strong password hashing

# Edit pg_hba.conf to use secure authentication methods
local   all             all                                     scram-sha-256
host    all             all             127.0.0.1/32            scram-sha-256
host    all             all             ::1/128                 scram-sha-256
```

### Secure Mail Servers

```bash
# Postfix Configuration
# Edit /etc/postfix/main.cf
smtpd_banner = $myhostname ESMTP
disable_vrfy_command = yes
smtpd_helo_required = yes
smtp_tls_security_level = may
smtpd_tls_security_level = may
smtp_tls_loglevel = 1
smtpd_tls_loglevel = 1
```

### Container Security

```bash
# Docker security best practices
# Run containers with minimal privileges
docker run --cap-drop=ALL --security-opt=no-new-privileges image_name

# Use user namespaces
dockerd --userns-remap=default

# Enable seccomp profiles
docker run --security-opt seccomp=/path/to/seccomp/profile.json image_name
```

## Kernel Hardening

### Secure Boot Parameters

```bash
# Edit /etc/default/grub
GRUB_CMDLINE_LINUX="audit=1 audit_backlog_limit=8192 apparmor=1 security=apparmor slab_nomerge slub_debug=FZP vsyscall=none page_alloc.shuffle=1 pti=on spectre_v2=on spec_store_bypass_disable=on mds=full,nosmt l1tf=full,nosmt"

# Update GRUB
update-grub  # Debian/Ubuntu
grub2-mkconfig -o /boot/grub2/grub.cfg  # Red Hat/CentOS
```

### Mandatory Access Control (MAC)

```bash
# Install and Configure AppArmor (Debian/Ubuntu)
apt install apparmor apparmor-utils
systemctl enable apparmor
aa-enforce /etc/apparmor.d/*

# Install and Configure SELinux (Red Hat/CentOS)
dnf install selinux-policy selinux-policy-targeted
setenforce 1
# Edit /etc/selinux/config
SELINUX=enforcing
```

### Limit Core Dumps

```bash
# Edit /etc/security/limits.conf
* hard core 0

# Edit /etc/sysctl.conf or create /etc/sysctl.d/99-security.conf
fs.suid_dumpable = 0
kernel.core_pattern = |/bin/false
```

### Restrict Kernel Module Loading

```bash
# Create /etc/modprobe.d/blacklist.conf
install usb-storage /bin/false
install cramfs /bin/false
install freevxfs /bin/false
install jffs2 /bin/false
install hfs /bin/false
install hfsplus /bin/false
install squashfs /bin/false
install udf /bin/false

# Apply changes
update-initramfs -u  # Debian/Ubuntu
dracut -f           # Red Hat/CentOS
```

## Logging and Monitoring

### Configure System Logging

```bash
# Install rsyslog if not present
apt install rsyslog  # Debian/Ubuntu
dnf install rsyslog  # Red Hat/CentOS

# Edit /etc/rsyslog.conf
# Send auth logs to separate file
auth,authpriv.*                 /var/log/auth.log

# Log all critical messages
*.crit                          /var/log/critical

# Configure remote logging
*.* @remote-syslog-server:514

# Restart rsyslog
systemctl restart rsyslog
```

### Set Up Auditd

```bash
# Install auditd
apt install auditd audispd-plugins  # Debian/Ubuntu
dnf install audit audit-libs        # Red Hat/CentOS

# Edit /etc/audit/auditd.conf
log_file = /var/log/audit/audit.log
max_log_file = 50
max_log_file_action = keep_logs
space_left = 75
space_left_action = email
action_mail_acct = root
admin_space_left = 50
admin_space_left_action = halt

# Add key audit rules in /etc/audit/rules.d/audit.rules
-w /etc/passwd -p wa -k identity
-w /etc/group -p wa -k identity
-w /etc/shadow -p wa -k identity
-w /etc/gshadow -p wa -k identity
-w /etc/sudoers -p wa -k actions
-w /etc/sudoers.d/ -p wa -k actions
-a always,exit -F arch=b64 -S execve -F euid=0 -F auid>=1000 -F auid!=4294967295 -k privileged

# Restart auditd
service auditd restart
```

### Implement Log Rotation

```bash
# Configure logrotate in /etc/logrotate.conf
# Default settings:
rotate 7
weekly
compress
delaycompress
notifempty
missingok

# Add specific configurations in /etc/logrotate.d/
```

### Set Up Intrusion Detection

```bash
# Install OSSEC
apt install ossec-hids  # Debian/Ubuntu

# Install Fail2ban
apt install fail2ban  # Debian/Ubuntu
dnf install fail2ban  # Red Hat/CentOS

# Configure Fail2ban for SSH
# Create /etc/fail2ban/jail.local
[sshd]
enabled = true
bantime = 1h
findtime = 10m
maxretry = 3
```

## Regular Maintenance

### Security Updates

```bash
# Debian/Ubuntu
apt update && apt upgrade -y
apt dist-upgrade -y

# Red Hat/CentOS
dnf update -y
```

### Check for Rootkits

```bash
# Install rkhunter
apt install rkhunter  # Debian/Ubuntu
dnf install rkhunter  # Red Hat/CentOS

# Update rkhunter database
rkhunter --update

# Run a check
rkhunter --check

# Install chkrootkit
apt install chkrootkit  # Debian/Ubuntu
dnf install chkrootkit  # Red Hat/CentOS

# Run chkrootkit
chkrootkit
```

### Check for Vulnerabilities

```bash
# Debian/Ubuntu
apt install debsecan
debsecan

# Red Hat/CentOS
dnf install openscap-scanner scap-security-guide
oscap oval eval --report report.html com.redhat.rhsa-all.xml
```

### Run System Integrity Checks

```bash
# Verify file integrity using AIDE
aide --check

# Verify package integrity
debsums -c  # Debian/Ubuntu
rpm -Va     # Red Hat/CentOS
```

## Security Tools

### Install Security Tools

```bash
# Basic security tools
apt install nmap lynis nikto rkhunter chkrootkit fail2ban ufw  # Debian/Ubuntu
dnf install nmap lynis rkhunter fail2ban firewalld # Red Hat/CentOS

# Run Lynis system audit
lynis audit system
```

### Configure Security Banners

```bash
# Edit /etc/issue and /etc/issue.net
# Example:
This system is restricted to authorized users only.
All activities are monitored and recorded.
Unauthorized access will be fully investigated and reported.

# Edit /etc/motd with similar message
```

## Compliance and Auditing

### Compliance Scanning

```bash
# Install OpenSCAP for compliance scanning
apt install openscap-scanner  # Debian/Ubuntu
dnf install openscap-scanner scap-security-guide  # Red Hat/CentOS

# Run a compliance scan
oscap xccdf eval --profile xccdf_org.ssgproject.content_profile_cis --results scan-results.xml --report scan-report.html /usr/share/xml/scap/ssg/content/ssg-ubuntu20-ds.xml  # Ubuntu 20.04
oscap xccdf eval --profile xccdf_org.ssgproject.content_profile_cis --results scan-results.xml --report scan-report.html /usr/share/xml/scap/ssg/content/ssg-rhel8-ds.xml  # RHEL 8
```

### Generate Audit Reports

```bash
# Use Lynis for security auditing and reports
lynis audit system --pentest --no-colors > security_audit_report.txt

# Generate PDF reports with assessment results
lynis audit system --auditor "Security Team" --report-file /tmp/lynis-report.dat
```

### Automated Security Tasks

```bash
# Set up cron jobs for regular security tasks
echo '0 3 * * * root /usr/sbin/rkhunter --update && /usr/sbin/rkhunter --check --skip-keypress --report-warnings-only | mail -s "Daily rkhunter run" root@localhost' > /etc/cron.d/rkhunter
echo '0 4 * * * root /usr/sbin/aide --check | mail -s "Daily AIDE run" root@localhost' > /etc/cron.d/aide
echo '0 5 * * 0 root /usr/sbin/lynis audit system --cronjob > /dev/null' > /etc/cron.d/lynis
```

## Final Checklist

1. ✅ Secure installation with proper partitioning
2. ✅ Up-to-date system with automatic updates
3. ✅ Minimal installed software
4. ✅ Strong user and password policies
5. ✅ SSH hardened with key-based authentication
6. ✅ Two-factor authentication configured
7. ✅ Secure file system configurations
8. ✅ File integrity monitoring active
9. ✅ Firewall enabled with restrictive rules
10. ✅ Secure kernel parameters
11. ✅ Unused services disabled
12. ✅ Critical services hardened
13. ✅ Comprehensive logging and monitoring
14. ✅ Regular security scans and audits

Remember that security is a continuous process. Regularly revisit and update your security measures to address new threats and vulnerabilities.
