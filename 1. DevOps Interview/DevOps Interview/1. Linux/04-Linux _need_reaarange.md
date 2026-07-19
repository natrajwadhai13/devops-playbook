---
title: "• 04-need_Reaarnge"
parent: "• Linux"
grand_parent: "• DevOps Interview"
grand_grand_parent: "1. DevOps"
nav_order: 4
---

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
