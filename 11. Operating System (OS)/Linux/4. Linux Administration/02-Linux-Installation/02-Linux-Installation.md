---
title: • 02-Linux-Installation
parent: • Linux
grand_parent: 11. Operating System (OS)
nav_order: 2
has_children: true
---

# Linux Installation

## Overview

Before learning Linux commands, you need a Linux environment to practice. The most common ways are Virtual Machines, Dual Boot, and Cloud Virtual Machines.

---

# VMware

VMware is virtualization software that allows you to run multiple operating systems on a single computer.

### Advantages

- Easy to use
- Good performance
- Snapshot support
- Best for learning and labs

---

# VirtualBox

VirtualBox is a free and open-source virtualization software developed by Oracle.

### Advantages

- Free
- Cross-platform
- Beginner friendly
- Supports Windows, Linux, and macOS

---

# VMware vs VirtualBox

| VMware             | VirtualBox         |
| ------------------ | ------------------ |
| Better Performance | Free & Open Source |
| Professional Use   | Best for Beginners |
| More Features      | Easy Installation  |

---

# Dual Boot

Dual Boot allows you to install two operating systems on the same computer.

**Example**

- Windows + Ubuntu
- Windows + Rocky Linux

### Advantages

- Better performance
- Full hardware access

### Disadvantages

- Requires disk partitioning
- Restart required to switch OS

---

# Cloud VM

A Cloud VM is a virtual machine hosted in the cloud.

### Popular Cloud Providers

- AWS EC2
- Azure Virtual Machine
- Google Cloud VM

### Advantages

- Real production environment
- Accessible from anywhere
- Used in DevOps and Cloud projects

---

# SSH Login

SSH (Secure Shell) is used to securely connect to a remote Linux server.

### Syntax

```bash
ssh username@server-ip
```

### Example

```bash
ssh ubuntu@192.168.1.10
```

---

# Hands-on Practice

## Install Ubuntu

- Download Ubuntu ISO
- Create a Virtual Machine
- Allocate CPU, RAM, and Disk
- Boot using the ISO
- Complete the installation

---

## Install Rocky Linux

- Download Rocky Linux ISO
- Create a Virtual Machine
- Allocate CPU, RAM, and Disk
- Boot using the ISO
- Complete the installation

---

## Create a Virtual Machine

Recommended Configuration:

| Resource | Value         |
| -------- | ------------- |
| CPU      | 2 Cores       |
| RAM      | 4 GB          |
| Disk     | 40 GB         |
| Network  | NAT / Bridged |

---

# Practice Tasks

- Install VMware or VirtualBox.
- Create one Ubuntu Virtual Machine.
- Create one Rocky Linux Virtual Machine.
- Login locally.
- Connect using SSH.
- Verify Internet connectivity using `ping`.

---

# Quick Interview Questions

### What is VMware?

A virtualization software used to run multiple operating systems on one computer.

### What is VirtualBox?

A free virtualization software developed by Oracle.

### What is Dual Boot?

Installing two operating systems on the same machine.

### What is a Cloud VM?

A virtual machine hosted by a cloud provider like AWS, Azure, or GCP.

### What is SSH?

A secure protocol used to connect to remote Linux servers.

---

# Key Takeaways

- VMware and VirtualBox are used to create virtual machines.
- Dual Boot provides better performance but requires restarting to switch OS.
- Cloud VMs are widely used in DevOps and production environments.
- SSH is the standard method to access remote Linux servers securely.
- Practice on both Ubuntu and Rocky Linux to gain hands-on experience.
