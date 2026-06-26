---
title: • Linux
parent: 11. Operating System (OS)
nav_order: 1
has_children: true
---

# Linux for DevOps Engineer

## (Basic → Advanced Complete Course)

**Duration:** 8–10 Weeks (Daily 2 Hours)

---

# Module 1 – Linux Fundamentals

### 1. Introduction

- What is Linux?
- Linux Architecture
- Kernel
- Shell
- GNU
- Linux Distributions
  - Ubuntu
  - RHEL
  - Rocky Linux
  - AlmaLinux
  - Debian

### 2. Installation

- VMware
- VirtualBox
- Dual Boot
- Cloud VM
- SSH Login

### Hands-on

- Install Ubuntu
- Install Rocky Linux
- Create Virtual Machines

---

# Module 2 – Linux Command Line

## File Commands

```bash
pwd
ls
cd
mkdir
touch
cp
mv
rm
find
locate
tree
```

## Viewing Files

```bash
cat
less
more
head
tail
nl
```

## Searching

```bash
grep
egrep
awk
sed
sort
uniq
cut
tr
```

## Compression

```bash
zip
unzip
gzip
gunzip
tar
xz
```

---

# Module 3 – Linux File System

- File System Hierarchy
- /
- /etc
- /home
- /boot
- /proc
- /sys
- /tmp
- /var
- /opt
- /usr
- /dev

### Hands-on

Explore every directory.

---

# Module 4 – File Permissions

- chmod
- chown
- chgrp
- umask
- Sticky Bit
- SGID
- SUID
- ACL

Practical exercises

---

# Module 5 – Users & Groups

```bash
useradd
usermod
passwd
groupadd
gpasswd
id
who
w
last
```

Password Policies

Sudo

Wheel Group

/etc/passwd

/etc/shadow

/etc/group

---

# Module 6 – Process Management

```bash
ps
top
htop
kill
killall
pkill
nice
renice
jobs
bg
fg
nohup
```

Hands-on

Kill process

Background jobs

CPU Priority

---

# Module 7 – Package Management

Ubuntu

```bash
apt
dpkg
```

RHEL

```bash
yum
dnf
rpm
```

Repositories

Install

Update

Remove

---

# Module 8 – Disk Management

Partition

fdisk

parted

mkfs

mount

umount

fstab

LVM

Swap

Disk Usage

```bash
df
du
lsblk
blkid
```

---

# Module 9 – Services

Systemd

```bash
systemctl
journalctl
service
```

Enable

Disable

Restart

Logs

Targets

---

# Module 10 – Boot Process

BIOS

UEFI

GRUB

Kernel

Initramfs

Systemd

Rescue Mode

Single User Mode

---

# Module 11 – Networking

OSI

TCP/IP

IPv4

CIDR

Subnet

Gateway

DNS

Routing

Commands

```bash
ip
ss
netstat
ping
dig
host
nslookup
curl
wget
nc
tcpdump
traceroute
```

Network Configuration

SSH

Hostname

Firewall

---

# Module 12 – SSH

SSH Login

SSH Config

SSH Keys

Passwordless Login

scp

sftp

ssh-agent

ssh-copy-id

---

# Module 13 – Logs

```bash
journalctl
dmesg
tail -f
less
grep
```

Important Logs

```
/var/log/messages

/var/log/secure

/var/log/syslog

/var/log/auth.log
```

---

# Module 14 – Cron Jobs

```bash
cron

crontab

at
```

Automation

Scheduling

Backup Script

---

# Module 15 – Shell Scripting

Variables

Conditions

Loops

Arrays

Functions

Arguments

Exit Codes

Error Handling

Real-world Scripts

- Backup
- User Creation
- Disk Monitoring
- Log Cleanup
- Health Check
- Service Restart
- Auto Deployment

---

# Module 16 – Security

SELinux

AppArmor

Firewalld

iptables Basics

SSH Security

Fail2Ban

Password Policies

Permissions

Security Hardening

---

# Module 17 – Performance Monitoring

```bash
top

htop

vmstat

iostat

sar

free

uptime

lsof

strace
```

CPU

RAM

Disk

Network

IO

---

# Module 18 – Storage

RAID Basics

NFS

SMB

Auto Mount

Network Storage

---

# Module 19 – Linux Troubleshooting

Server Not Booting

Disk Full

High CPU

High Memory

Permission Issues

Network Down

DNS Issue

Package Failure

Service Failure

SSH Not Working

Filesystem Read Only

---

# Module 20 – Linux for DevOps

Git Installation

Docker Installation

Kubernetes Installation

Java Installation

Maven

Python

NodeJS

Nginx

Apache

Tomcat

Jenkins

Ansible

Terraform

Helm

kubectl

AWS CLI

Azure CLI

---

# Module 21 – Web Servers

Apache

Nginx

Virtual Hosts

SSL

Reverse Proxy

Load Balancer

Logs

Performance

---

# Module 22 – DevOps Real Server Tasks

- Create New Linux Server
- Create Users
- Configure SSH
- Configure Firewall
- Install Java
- Install Docker
- Install Git
- Configure Jenkins
- Install Nginx
- SSL Setup
- Reverse Proxy
- Backup
- Monitoring
- Log Rotation
- Troubleshooting
- Deployment Script

---

# Module 23 – Interview Preparation

### Linux Interview Questions

- 200 Basic Questions
- 200 Intermediate Questions
- 200 Advanced Questions
- Production Scenarios
- Troubleshooting Scenarios

---

# Module 24 – Final Project

Build a Production Linux Server

- Install Linux
- Configure Network
- Secure SSH
- Create Users
- Configure Firewall
- Install Git
- Install Docker
- Install Jenkins
- Install Nginx
- SSL Configuration
- Deploy Website
- Monitoring
- Backup
- Automation
- Troubleshooting

---

# Skills You'll Gain

After completing this course, you will be able to:

- ✅ Manage Linux servers confidently
- ✅ Troubleshoot production issues
- ✅ Write Bash automation scripts
- ✅ Configure networking and security
- ✅ Install and manage DevOps tools
- ✅ Deploy applications on Linux
- ✅ Work with cloud Linux instances (AWS, Azure, GCP)
- ✅ Handle real-world DevOps administration tasks

This syllabus aligns well with the skills emphasized in professional Linux administration tracks such as RHCSA and Linux Foundation courses, while extending them with DevOps-specific topics like Docker, Kubernetes, CI/CD tools, automation, and production troubleshooting. ([Red Hat][1])

This Linux course should be the **foundation** before moving on to Git, Docker, Kubernetes, Terraform, Ansible, Jenkins, and cloud platforms.

[1]: https://www.redhat.com/en/services/certification/rhcsa?utm_source=chatgpt.com "Red Hat Certified System Administrator"
