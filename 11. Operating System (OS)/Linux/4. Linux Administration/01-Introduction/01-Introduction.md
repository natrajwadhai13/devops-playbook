---
title: • 01-Introducation
parent: • Linux
grand_parent: 11. Operating System (OS)
nav_order: 1.4
has_children: true
---

# Introduction

## What is Linux?

Linux is a **free, open-source operating system** based on the Unix architecture. It is widely used for **servers, cloud computing, DevOps, containers, networking, and embedded systems**.

**Why Linux?**

- Free and Open Source
- Secure and Stable
- High Performance
- Multi-user & Multitasking
- Preferred OS for DevOps and Cloud

---

## Linux Architecture

Linux follows a layered architecture:

```text
+---------------------------+
|        Applications       |
+---------------------------+
|          Shell            |
+---------------------------+
|          Kernel           |
+---------------------------+
|        Hardware           |
+---------------------------+
```

### Components

- **Applications** – User programs (Git, Docker, Nginx, etc.)
- **Shell** – Interface between user and kernel.
- **Kernel** – Core of the operating system.
- **Hardware** – CPU, RAM, Disk, Network devices.

---

## Kernel

The **Kernel** is the heart of Linux.

### Responsibilities

- Process Management
- Memory Management
- File System Management
- Device Management
- Network Management
- Security

**Example:**
When you run `ls`, the shell sends the request to the kernel, and the kernel reads the file system and returns the result.

---

## Shell

The **Shell** is a command-line interpreter that allows users to interact with the Linux system.

**Popular Shells**

- Bash (Most Common)
- Zsh
- Ksh
- Fish
- Sh

**Example**

```bash
pwd
ls -l
mkdir demo
```

---

## GNU

**GNU (GNU's Not Unix)** is a collection of free software that provides essential Linux utilities.

Examples:

- `bash`
- `gcc`
- `grep`
- `sed`
- `awk`
- `coreutils`

**Note:** Linux = **Kernel + GNU Tools**

---

# Linux Distributions

A Linux Distribution (Distro) is a complete operating system built using the Linux kernel with additional software and package managers.

| Distribution | Best For                      |
| ------------ | ----------------------------- |
| Ubuntu       | Beginners, Cloud, DevOps      |
| RHEL         | Enterprise Production Servers |
| Rocky Linux  | Free RHEL Alternative         |
| AlmaLinux    | Enterprise & Hosting          |
| Debian       | Stable Servers & Development  |

---

## Ubuntu

- Beginner Friendly
- Large Community
- Uses **APT** package manager
- Popular on AWS, Azure, and GCP

---

## RHEL (Red Hat Enterprise Linux)

- Enterprise Linux
- Paid Subscription
- Commercial Support
- Used by large organizations

---

## Rocky Linux

- Free replacement for RHEL
- Enterprise-grade stability
- Popular in production environments

---

## AlmaLinux

- Community-driven RHEL-compatible distribution
- Stable and secure
- Commonly used in hosting environments

---

## Debian

- One of the oldest Linux distributions
- Highly stable
- Uses **APT**
- Base for Ubuntu

---

# Quick Interview Questions

### What is Linux?

A free and open-source Unix-like operating system.

### What is the Kernel?

The core component that manages hardware and system resources.

### What is the Shell?

A command-line interface used to communicate with the kernel.

### What is GNU?

A collection of free software and utilities used with the Linux kernel.

### Name some Linux distributions.

- Ubuntu
- RHEL
- Rocky Linux
- AlmaLinux
- Debian

---

# Key Takeaways

- Linux is the most popular operating system for servers and DevOps.
- Kernel manages hardware and system resources.
- Shell provides a command-line interface.
- GNU provides essential Linux tools.
- Ubuntu is ideal for learning, while RHEL, Rocky Linux, and AlmaLinux are widely used in enterprise environments.

======================================================

## 🧭 Linux Roadmap for DevOps Engineers (4–6 Weeks)

---

### 🧱 Phase 1: Core Linux Foundation (Week 1)

#### 🎯 Focus:

- Day-to-day Linux usage, terminal efficiency

#### ✅ Topics:

- Linux file system layout (`/etc`, `/var`, `/home`, `/proc`, etc.)
- File operations: `ls`, `cp`, `mv`, `rm`, `find`, `locate`, `du`, `df`
- Directory navigation: `cd`, `pwd`, `tree`
- File permissions: `chmod`, `chown`, `umask`, `groups`
- Users & groups: `useradd`, `passwd`, `usermod`, `groupadd`
- Text editors: `vim`, `nano`, `cat`, `more`, `less`, `head`, `tail`
- Viewing system info: `uname`, `uptime`, `free`, `top`, `htop`, `vmstat`

---

### 🧱 Phase 2: Shell Mastery + Bash Scripting (Week 2)

#### 🎯 Focus:

- Scripting automation & command chaining

#### ✅ Topics:

- Environment variables: `$PATH`, `$HOME`, `$USER`
- Shell scripting basics (already covered)
- Input/output redirection: `>`, `>>`, `<`, `2>`, `&>`
- Pipes (`|`) and filters: `grep`, `awk`, `sed`, `cut`, `sort`, `uniq`
- Command substitution: `` `command` `` or `$(command)`
- Crontab for job scheduling
- Aliases, functions, and `.bashrc`

---

### 🧱 Phase 3: System Administration Essentials (Week 3)

#### 🎯 Focus:

- Manage Linux like an admin

#### ✅ Topics:

- Process management: `ps`, `top`, `kill`, `nice`, `renice`, `jobs`, `bg`, `fg`
- Package management:
  - Ubuntu/Debian: `apt`, `dpkg`
  - RHEL/CentOS/Amazon Linux: `yum`, `dnf`, `rpm`

- Service management: `systemctl`, `service`, `chkconfig`
- Disk management: `lsblk`, `fdisk`, `mount`, `umount`, `df`, `du`
- Log monitoring: `/var/log/syslog`, `/var/log/messages`, `/var/log/nginx/*`
- Uptime, memory, CPU usage, and bottleneck detection

---

### 🧱 Phase 4: Networking in Linux (Week 4)

#### 🎯 Focus:

- Debugging and managing Linux networking

#### ✅ Topics:

- IP addressing: `ip addr`, `ifconfig`, `ip a`, `ip link`
- Hostname and DNS: `/etc/hosts`, `/etc/resolv.conf`
- Network troubleshooting: `ping`, `traceroute`, `netstat`, `ss`, `tcpdump`
- Firewall basics: `ufw`, `iptables`, `firewalld`
- Port testing: `nc`, `telnet`, `curl`
- Checking open/listening ports: `netstat -tulpn` or `ss -ltnp`

---

### 🧱 Phase 5: DevOps & Cloud Integration (Week 5–6)

#### 🎯 Focus:

- Prepare Linux for Docker, Jenkins, Git, Ansible, AWS

#### ✅ Topics:

- Manage Linux on EC2 (Amazon Linux 2 / 2023)
- Install and configure:
  - Docker & Docker Compose
  - Git & Git hooks
  - Ansible agents and inventory
  - Jenkins agent

- Set up reverse proxy: `nginx` or `apache2`
- Monitor disk, memory, cron logs, service status for CI/CD health

---

## 🧪 Real-World Tasks to Practice

| Task                                      | Tools/Commands                     |
| ----------------------------------------- | ---------------------------------- |
| Create user and give sudo                 | `useradd`, `usermod`, `sudo`       |
| Set up a cronjob to back up logs          | `crontab`, `tar`, `date`           |
| Configure static IP on a server           | `nmcli`, `/etc/network/interfaces` |
| Monitor nginx logs and extract 500 errors | `grep`, `awk`, `cut`               |
| Set up SSH key-based login                | `ssh-keygen`, `ssh-copy-id`        |

---

## 📘 Must-Know Linux Files for DevOps

| File Path                      | Purpose             |
| ------------------------------ | ------------------- |
| `/etc/passwd`                  | User accounts       |
| `/etc/shadow`                  | Encrypted passwords |
| `/etc/sudoers`                 | Sudo permissions    |
| `/etc/fstab`                   | Mount points        |
| `/etc/hosts`                   | Local DNS           |
| `/var/log/syslog` / `messages` | System logs         |
| `/etc/crontab`                 | Scheduled jobs      |

---

## 📦 Tools to Pair With Linux for DevOps

| Tool          | Why Important                     |
| ------------- | --------------------------------- |
| **Git**       | Source control, automated deploys |
| **Jenkins**   | CI/CD pipelines on Linux servers  |
| **Docker**    | Run apps in containers            |
| **Ansible**   | Remote automation via SSH         |
| **AWS CLI**   | Scripted interaction with cloud   |
| **Systemd**   | Manage and debug services         |
| **Logrotate** | Manage log files efficiently      |

---

## 📚 Learning Resources

- [LinuxJourney](https://linuxjourney.com/)
- [tldr.sh](https://tldr.sh/)
- [The Art of Command Line](https://github.com/jlevy/the-art-of-command-line)
- [OverTheWire: Bandit](https://overthewire.org/wargames/bandit/) (great practice)

---

## 🧠 Final Tips

- Set up a Linux VM (or use Amazon EC2 free tier)
- Maintain a personal `.bashrc` or `.zshrc` config
- Automate one task per week using Bash/Linux
- Monitor your own machine using CLI tools

---
