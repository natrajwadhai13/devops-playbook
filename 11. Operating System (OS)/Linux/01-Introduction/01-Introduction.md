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
