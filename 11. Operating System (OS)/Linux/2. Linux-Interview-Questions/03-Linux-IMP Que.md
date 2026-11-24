---
title: "• Linux-IMP Que"
parent: "• Linux-Interview-QA"
grand_parent: • Linux
grand_grand_parent: 11. Operating System (OS)
nav_order: 1
has_children: true
---


=========================

## **Linux-IMP Que**


===========================================




# Chapter 1 - Linux Basics (100 Interview Questions)

> Beginner to Advanced Linux Basics for Interview Preparation

---

# 📌 Jump to Section

* [1. Introduction to Linux (Q1–Q10)](#1-introduction-to-linux)
* [2. Linux History (Q11–Q15)](#2-linux-history)
* [3. Linux Architecture (Q16–Q25)](#3-linux-architecture)
* [4. Linux Kernel (Q26–Q35)](#4-linux-kernel)
* [5. Linux Shell (Q36–Q45)](#5-linux-shell)
* [6. Bash (Q46–Q50)](#6-bash)
* [7. Linux Distributions (Q51–Q60)](#7-linux-distributions)
* [8. Linux Features (Q61–Q70)](#8-linux-features)
* [9. Root User & Privileges (Q71–Q80)](#9-root-user--privileges)
* [10. Common Interview Questions (Q81–Q100)](#10-common-interview-questions)

---

# 1. Introduction to Linux

## Questions

Q1. What is Linux?

Q2. Who invented Linux?

Q3. When was Linux developed?

Q4. Why was Linux created?

Q5. Is Linux an Operating System or a Kernel?

Q6. Why is Linux called Open Source?

Q7. What are the advantages of Linux?

Q8. What are the disadvantages of Linux?

Q9. Where is Linux used?

Q10. Why do companies prefer Linux servers?

---

# 2. Linux History

## Questions

Q11. Explain the history of Linux.

Q12. What is GNU?

Q13. Difference between GNU and Linux?

Q14. What is GPL License?

Q15. Explain Open Source Software.

---

# 3. Linux Architecture

## Questions

Q16. Explain Linux Architecture.

Q17. Explain User Space and Kernel Space.

Q18. Components of Linux Architecture.

Q19. What is System Call?

Q20. What is Hardware Abstraction?

Q21. Explain Linux Layers.

Q22. Explain User → Shell → Kernel → Hardware.

Q23. What happens when a command is executed?

Q24. Explain command execution flow.

Q25. Draw Linux Architecture.

---

# 4. Linux Kernel

## Questions

Q26. What is Kernel?

Q27. Types of Kernel.

Q28. Monolithic Kernel.

Q29. Microkernel.

Q30. Hybrid Kernel.

Q31. Kernel Responsibilities.

Q32. Kernel Modules.

Q33. How to check Kernel Version?

Q34. Kernel Upgrade.

Q35. Kernel Panic.

---

# 5. Linux Shell

## Questions

Q36. What is Shell?

Q37. Types of Shell.

Q38. Bash vs Sh.

Q39. Bash vs Ksh.

Q40. Bash vs Zsh.

Q41. Login Shell.

Q42. Non-login Shell.

Q43. Interactive Shell.

Q44. Non-interactive Shell.

Q45. Default Shell.

---

# 6. Bash

## Questions

Q46. What is Bash?

Q47. Bash Features.

Q48. Bash Variables.

Q49. Environment Variables.

Q50. Alias.

---

# 7. Linux Distributions

## Questions

Q51. What is Linux Distribution?

Q52. RHEL.

Q53. CentOS.

Q54. Ubuntu.

Q55. Debian.

Q56. SUSE.

Q57. Fedora.

Q58. Rocky Linux.

Q59. AlmaLinux.

Q60. Difference between RHEL and Ubuntu.

---

# 8. Linux Features

## Questions

Q61. Multi-user.

Q62. Multitasking.

Q63. Multiprocessing.

Q64. Portability.

Q65. Security.

Q66. Stability.

Q67. Scalability.

Q68. Reliability.

Q69. Networking Features.

Q70. File System Support.

---

# 9. Root User & Privileges

## Questions

Q71. What is Root User?

Q72. What is Superuser?

Q73. UID 0.

Q74. Root vs Normal User.

Q75. sudo vs su.

Q76. Why avoid Root Login?

Q77. Root Password.

Q78. Root Home Directory.

Q79. Principle of Least Privilege.

Q80. Best Practices.

---

# 10. Common Interview Questions

## Questions

Q81. Linux vs Unix.

Q82. Linux vs Windows.

Q83. Advantages over Windows.

Q84. Can Linux get viruses?

Q85. Why is Linux secure?

Q86. Is Linux free?

Q87. Explain Open Source.

Q88. Which Linux distributions have you worked on?

Q89. Which Linux versions have you used?

Q90. What is your current Linux environment?

Q91. Why did your company choose Linux?

Q92. Which Linux commands do you use daily?

Q93. Explain your production environment.

Q94. How many Linux servers have you managed?

Q95. How do you connect to Linux servers?

Q96. Explain your daily Linux activities.

Q97. What is your Linux experience?

Q98. Which Linux certification do you have?

Q99. Why should we hire you as a Linux Administrator?

Q100. Tell us about your Linux project experience.

=============================================

### need_Reaarnge


# 🔥 LINUX INTERVIEW QUESTIONS & ANSWERS (6+ YEARS EXPERIENCE)

---

## 🔹 SYSTEM & ARCHITECTURE

### 1. Explain the Linux boot process in detail.

**Answer:**

1. **BIOS/UEFI** – Hardware initialization
2. **MBR / GPT** – Finds bootloader
3. **GRUB** – Loads kernel and initramfs
4. **Kernel** – Initializes hardware, mounts root filesystem
5. **init / systemd** – Starts system services

---

### 2. What is `systemd` and why is it used?

**Answer:**
`systemd` is the **init system** that manages services, targets, logging, and dependencies.
It is faster and dependency-aware compared to SysVinit.

---

### 3. Difference between Runlevels and Targets?

**Answer:**
Runlevels are legacy (SysV), targets are used by **systemd**.

Example:

```bash
multi-user.target → runlevel 3
graphical.target → runlevel 5
```

---

## 🔹 PERFORMANCE & TROUBLESHOOTING

### 4. Production server is slow. What will you check first?

**Answer:**

1. CPU → `top`, `htop`
2. Memory → `free -m`
3. Disk → `df -h`, `iostat`
4. Processes → `ps aux`
5. Logs → `/var/log/messages`, `journalctl`

---

### 5. How do you find which process is consuming high memory?

**Answer:**

```bash
ps aux --sort=-%mem | head
```

---

### 6. What is load average?

**Answer:**
Load average shows the **number of processes waiting for CPU** in:

- 1 minute
- 5 minutes
- 15 minutes

Rule:

- Load ≤ CPU cores → OK
- Load > CPU cores → Performance issue

---

### 7. Difference between CPU idle and CPU wait?

**Answer:**

- **Idle:** CPU has nothing to do
- **I/O wait:** CPU waiting for disk/network operations

---

## 🔹 PROCESS & MEMORY MANAGEMENT

### 8. What is a zombie process?

**Answer:**
A zombie process has completed execution but still exists because the parent hasn’t read its exit status.

---

### 9. How do you kill a stuck process?

**Answer:**

```bash
kill -9 PID
```

---

### 10. What is OOM Killer?

**Answer:**
When memory is exhausted, Linux automatically kills processes using **OOM Killer** to keep the system alive.

---

## 🔹 FILESYSTEM & STORAGE

### 11. Difference between ext4 and xfs?

**Answer:**

| ext4                 | xfs                  |
| -------------------- | -------------------- |
| General purpose      | High performance     |
| Good for small files | Best for large files |
| Slower resize        | Fast scaling         |

---

### 12. How do you extend a filesystem without downtime?

**Answer:**
For LVM:

```bash
lvextend -L +10G /dev/vg/lv
resize2fs /dev/vg/lv
```

---

### 13. What is inode?

**Answer:**
Inode stores **metadata** (permissions, owner, size, pointers).
Filename is NOT stored in inode.

---

### 14. Disk is free but system says “No space left”. Why?

**Answer:**

- Inode exhaustion
- Hidden deleted files still open by processes
  Check:

```bash
df -i
lsof | grep deleted
```

---

## 🔹 NETWORKING

### 15. How do you troubleshoot network connectivity issues?

**Answer:**

1. `ip a` – Interface up/down
2. `ping` – Connectivity
3. `ss -tulnp` – Ports
4. `traceroute` – Path
5. Firewall – `iptables`, `firewalld`

---

### 16. Difference between `netstat` and `ss`?

**Answer:**
`ss` is faster and modern replacement of `netstat`.

---

### 17. How do you check which port a service is listening on?

**Answer:**

```bash
ss -tulnp
```

---

## 🔹 SECURITY

### 18. How do you secure a Linux server?

**Answer:**

- Disable root login
- Use SSH key authentication
- Firewall rules
- Regular patching
- SELinux/AppArmor
- Audit logs

---

### 19. Difference between SELinux and AppArmor?

**Answer:**

- SELinux → Label-based (more secure, complex)
- AppArmor → Path-based (easier to manage)

---

### 20. How do you find who logged into the server?

**Answer:**

```bash
last
who
```

---

## 🔹 LOGGING & MONITORING

### 21. Where are system logs stored?

**Answer:**

- `/var/log/messages`
- `/var/log/syslog`
- `journalctl` (systemd)

---

### 22. How do you monitor logs in real time?

**Answer:**

```bash
tail -f /var/log/messages
journalctl -f
```

---

## 🔹 AUTOMATION & SCRIPTING

### 23. Difference between `cron` and `systemd timers`?

**Answer:**

- Cron → Time-based
- systemd timer → Dependency-based, more reliable

---

### 24. Write a script to check disk usage and alert if >80%.

**Answer:**

```bash
#!/bin/bash
usage=$(df / | awk 'NR==2 {print $5}' | cut -d% -f1)
if [ $usage -gt 80 ]; then
  echo "Disk usage critical"
fi
```

---

## 🔹 REAL PRODUCTION SCENARIOS

### 25. Server rebooted automatically. How will you investigate?

**Answer:**

- `last reboot`
- Check logs → `journalctl -b -1`
- Check OOM → `/var/log/messages`
- Hardware alerts / cloud events

---

### 26. Application works manually but fails via cron. Why?

**Answer:**

- Cron has limited PATH
- Environment variables missing
- Permission issues

---

### 27. How do you debug a service not starting?

**Answer:**

```bash
systemctl status service
journalctl -u service
```

---
