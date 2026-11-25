---
title: "• Difference RHEL"
parent: "• Linux-Interview-QA"
grand_parent: • Linux
grand_grand_parent: 11. Operating System (OS)
nav_order: 3
has_children: true
---

# **Difference Between RHEL 6, 7, 8, 9 and 10**

## **1. Major Version Comparison**

| Features           | RHEL 6             | RHEL 7             | RHEL 8             | RHEL 9             | RHEL 10            |
| ------------------ | ------------------ | ------------------ | ------------------ | ------------------ | ------------------ |
| Kernel             | 2.6.32             | 3.10               | 4.18               | 5.14               | 6.12               |
| Init System        | SysVinit / Upstart | systemd            | systemd            | systemd            | systemd            |
| Package Manager    | YUM                | YUM                | DNF 4              | DNF 4              | DNF 5              |
| Default Python     | 2.6                | 2.7                | 3.6                | 3.9                | 3.12               |
| Default Filesystem | ext4               | XFS                | XFS                | XFS                | XFS                |
| Firewall           | iptables           | firewalld          | firewalld          | firewalld          | firewalld          |
| Firewall Backend   | iptables           | iptables           | nftables           | nftables           | nftables           |
| Cgroups            | v1                 | v1                 | v1 (default)       | v2 (default)       | v2 only            |
| Bootloader         | GRUB Legacy        | GRUB2              | GRUB2              | GRUB2              | GRUB2              |
| Network Management | Network Scripts    | NetworkManager     | NetworkManager     | NetworkManager     | NetworkManager     |
| Logging            | rsyslog            | rsyslog + journald | rsyslog + journald | rsyslog + journald | rsyslog + journald |

Kernel versions are original major-release baselines, not current running kernel versions. Python refers to the default implementation, not every available version.&#x20;

## **2. Important Changes Between Versions**

**RHEL 6 → RHEL 7**

- SysVinit replaced by systemd.
- GRUB Legacy replaced by GRUB2.
- XFS became the default filesystem.
- firewalld introduced as the firewall management service.
- NetworkManager became the preferred network management tool.

**RHEL 7 → RHEL 8**

- YUM technology moved to DNF.
- BaseOS and AppStream repositories introduced.
- Python 3 introduced as the default Python implementation.
- nftables became the default firewall backend.
- Network scripts deprecated.

**RHEL 8 → RHEL 9**

- Kernel upgraded from 4.18 to 5.14.
- cgroups v2 became default.
- Python 3.9 became default.
- OpenSSL 3.0 introduced.
- Network scripts removed.

**RHEL 9 → RHEL 10**

- Kernel upgraded from 5.14 to 6.12.
- DNF 5 introduced.
- Python 3.12 became default.
- cgroups v1 support removed.
- Network team (teamd) removed; use bonding instead.
- RHEL Lightspeed AI command-line assistant available.

[image](https://www.google.com/s2/favicons?domain=https://docs.redhat.com&sz=32)

Red Hat Documentation+3

## **3. Most Important Interview Differences**

इन differences को practical commands के साथ याद रखें:

| Interview Question        | RHEL 6                  | RHEL 7–10      |
| ------------------------- | ----------------------- | -------------- |
| How to manage services?   | `service` / `chkconfig` | `systemctl`    |
| How to manage firewall?   | `iptables`              | `firewall-cmd` |
| How to manage network?    | Network scripts         | `nmcli`        |
| How to check logs?        | `/var/log/messages`     | `journalctl`   |
| How to manage bootloader? | GRUB                    | GRUB2          |
| How to install packages?  | `yum`                   | `yum` / `dnf`  |
| How to check IP?          | `ifconfig`              | `ip addr`      |
| How to check routes?      | `route -n`              | `ip route`     |

## **4. Important Commands for All Versions**

```
# OS version
cat /etc/redhat-release

# Kernel version
uname -r

# Init system
ps -p 1 -o comm=

# Package manager
yum --version
dnf --version

# Filesystem
df -Th

# Network
ip addr
nmcli device status

# Firewall
systemctl status firewalld

# SELinux
getenforce

# System logs
journalctl -xe

# Bootloader
grub2-mkconfig --version
```

## **5. Interview में ये 10 Questions जरूर तैयार करें**

1. What is the major difference between RHEL 6 and RHEL 7?
2. Why was systemd introduced in RHEL 7?
3. What is the difference between YUM and DNF?
4. What is BaseOS and AppStream in RHEL 8?
5. What is the difference between iptables and nftables?
6. What is the difference between cgroups v1 and v2?
7. Why was XFS introduced as the default filesystem in RHEL 7?
8. What happened to network-scripts in RHEL 8 and RHEL 9?
9. What are the major changes in RHEL 10?
10. How do you migrate RHEL 7 to RHEL 8 and RHEL 8 to RHEL 9?

## **6. Quick Revision – Remember These**

**RHEL 6**

Legacy

SysVinit, YUM, GRUB, ext4, iptables

**RHEL 7**

systemd, XFS, firewalld, GRUB2

**RHEL 8**

DNF, BaseOS, AppStream, Python 3

**RHEL 9**

cgroups v2, OpenSSL 3, Python 3.9

**RHEL 10**

DNF 5, Python 3.12, cgroups v2 only, Lightspeed AI
