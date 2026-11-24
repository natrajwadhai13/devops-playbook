---
title: • 04-Linux-File-System
parent: • Linux
grand_parent: 11. Operating System (OS)
nav_order: 4
has_children: true
---

# Linux File System

## Overview

The Linux File System organizes all files and directories in a **hierarchical structure**. Everything in Linux starts from the **root (`/`) directory**.

---

# File System Hierarchy

```text
/
├── bin
├── boot
├── dev
├── etc
├── home
├── lib
├── media
├── mnt
├── opt
├── proc
├── root
├── run
├── sbin
├── srv
├── sys
├── tmp
├── usr
└── var
```

---

# Important Directories

## `/` (Root)

- Top-level directory
- Parent of all directories
- Everything starts from here

---

## `/etc`

Configuration files for the system and applications.

**Examples**

- `/etc/passwd`
- `/etc/hosts`
- `/etc/fstab`
- `/etc/ssh/`

---

## `/home`

Home directory for normal users.

**Example**

```text
/home/natraj
/home/user1
```

---

## `/boot`

Contains boot loader and Linux kernel files.

**Examples**

- Kernel (`vmlinuz`)
- GRUB files
- initramfs

---

## `/proc`

Virtual file system containing **process and kernel information**.

**Examples**

```bash
cat /proc/cpuinfo
cat /proc/meminfo
```

---

## `/sys`

Virtual file system containing **hardware and kernel device information**.

Used for kernel tuning and hardware management.

---

## `/tmp`

Temporary files.

- Automatically cleaned after reboot.
- Any user can write here.

---

## `/var`

Variable data that changes frequently.

**Examples**

- Logs
- Cache
- Mail
- Databases

Common directory:

```text
/var/log
```

---

## `/opt`

Optional third-party software.

**Examples**

- Google Chrome
- Custom applications
- Enterprise software

---

## `/usr`

Contains user applications, libraries, and documentation.

Common directories:

```text
/usr/bin
/usr/sbin
/usr/lib
/usr/share
```

---

## `/dev`

Contains device files.

**Examples**

```text
/dev/sda
/dev/null
/dev/tty
```

Everything in Linux is treated as a file, including hardware devices.

---

# Hands-on Practice

Explore each directory:

```bash
ls /
ls /etc
ls /home
ls /boot
ls /proc
ls /sys
ls /tmp
ls /var
ls /opt
ls /usr
ls /dev
```

Check your current directory:

```bash
pwd
```

Navigate between directories:

```bash
cd /etc
cd /home
cd /var/log
```

---

# Quick Interview Questions

### What is the root directory?

The `/` directory is the top-level directory in Linux.

### Which directory contains configuration files?

`/etc`

### Where are user home directories stored?

`/home`

### Which directory stores log files?

`/var/log`

### Which directory contains device files?

`/dev`

### Which directory contains temporary files?

`/tmp`

### Which directory stores boot files?

`/boot`

---

# Key Takeaways

- Linux follows a hierarchical file system starting from `/`.
- `/etc` stores configuration files.
- `/home` contains user data.
- `/boot` contains boot-related files.
- `/var` stores logs and changing data.
- `/proc` and `/sys` provide system and kernel information.
- `/dev` represents hardware devices as files.
- Understanding the Linux File System is essential for Linux Administration and DevOps.
