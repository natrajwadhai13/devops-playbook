---
title: "• 02-Linux-Troubleshooting"
parent: "• Linux"
grand_parent: "• DevOps Interview"
grand_grand_parent: "1. DevOps"
nav_order: 2
---

# 🛠 Linux Troubleshooting & Production Scenarios (250+)

> Complete Linux Production Support, Incident Handling & Root Cause Analysis Guide

---

# 📌 Jump to Section

## 01. Boot Issues

- Server not booting
- GRUB issues
- Kernel panic
- Emergency mode
- Rescue mode
- Initramfs prompt
- Boot loop
- Root password reset
- Boot partition full
- Missing initramfs

---

## 02. Login Issues

- Unable to login
- Root login disabled
- SSH key authentication failure
- Password expired
- Account locked
- PAM issues
- Home directory missing
- Incorrect shell
- SELinux login denial
- Authentication failures

---

## 03. SSH Issues

- SSH service not running
- Connection refused
- Connection timed out
- Permission denied (publickey)
- Host key verification failed
- SSH hangs after login
- SSH slow response
- MaxSessions reached
- Port changed
- Firewall blocking SSH

---

## 04. Network Issues

- Network unreachable
- No internet
- NIC down
- Wrong gateway
- Wrong subnet mask
- Duplicate IP
- Interface not coming up
- MTU issues
- VLAN issues
- Bonding issues
- Packet loss
- High latency

---

## 05. DNS Issues

- DNS not resolving
- Slow DNS
- Wrong nameserver
- /etc/resolv.conf overwritten
- /etc/hosts conflicts
- Reverse DNS failure
- Search domain issues

---

## 06. NFS Issues

- NFS mount failed
- Stale file handle
- Permission denied
- exportfs configuration issue
- Firewall blocking NFS
- RPC service down
- NFS version mismatch
- Read-only mount

---

## 07. Storage & Disk Issues

- Disk full
- Root filesystem full
- /boot full
- /var full
- /tmp full
- Inode full
- Filesystem read-only
- Corrupted filesystem
- Mount failure
- UUID changed
- Bad sectors
- Disk replacement
- SCSI disk rescan

---

## 08. LVM Issues

- VG not found
- LV inactive
- PV missing
- Extend LVM
- Reduce LVM
- Snapshot full
- UUID conflict
- Filesystem resize failed

---

## 09. Memory Issues

- High memory usage
- Memory leak
- OOM Killer
- Swap full
- Swap not active
- Buffer/cache confusion
- Shared memory issues
- HugePages

---

## 10. CPU Issues

- CPU 100%
- High system CPU
- High user CPU
- High iowait
- Run queue high
- Load average high
- CPU affinity
- Context switching

---

## 11. Performance Issues

- Server slow
- Application slow
- High load average
- Disk I/O bottleneck
- Slow filesystem
- Network latency
- Process consuming resources
- Hung tasks

---

## 12. Kernel Issues

- Kernel panic
- Kernel crash
- Kernel upgrade failed
- Module missing
- Driver issue
- dmesg errors
- Soft lockup
- Hard lockup

---

## 13. SELinux Issues

- SELinux blocking application
- AVC denials
- Context mismatch
- Boolean configuration
- Relabel filesystem
- Restorecon usage

---

## 14. Firewall Issues

- Service blocked
- Port closed
- firewalld not running
- Rich rules
- Zone configuration
- iptables conflict

---

## 15. Package Management Issues

- RPM database corruption
- Dependency conflict
- Repository unavailable
- Package verification failure
- Patch rollback
- GPG key issues
- Broken package installation

---

## 16. Service Issues

- Service failed to start
- Service crashes
- Restart loop
- Dependency failure
- Wrong permissions
- Missing configuration
- Port already in use

---

## 17. Application Issues

- Apache down
- Nginx down
- Tomcat down
- Java application failure
- Database connection issue
- SSL certificate expired
- Reverse proxy issue
- High response time

---

## 18. Logs & Monitoring

- journalctl analysis
- /var/log/messages
- secure log
- audit log
- dmesg
- last reboot
- system resource analysis

---

## 19. Security Issues

- Brute-force attack
- Unauthorized login
- File permission issue
- SUID vulnerability
- Malware detection
- Rootkit checks
- SSH hardening

---

## 20. Incident Handling

- Incident response process
- Severity levels
- Impact assessment
- Service restoration
- Customer communication
- Escalation process
- Post-incident review

---

## 21. Root Cause Analysis (RCA)

- What is RCA?
- How to perform RCA?
- Data collection
- Timeline creation
- Identify root cause
- Corrective action
- Preventive action
- RCA report writing

---

# ⭐ Top 50 Interview Scenarios

1. Server not booting after patching.
2. Root filesystem is 100%.
3. "No space left on device" but `df -h` shows free space.
4. Inode usage is 100%.
5. SSH is not working after reboot.
6. DNS resolution suddenly stopped.
7. CPU utilization is constantly above 95%.
8. Memory usage reaches 100%.
9. Load average is very high.
10. Swap usage is 100%.
11. Server becomes slow every afternoon.
12. Application port is already in use.
13. Apache service fails to start.
14. Nginx returns 502 Bad Gateway.
15. SELinux blocks application access.
16. firewalld blocks incoming traffic.
17. Package installation fails due to dependencies.
18. RPM database is corrupted.
19. Yum repository is unreachable.
20. Server stuck in emergency mode.
21. GRUB prompt appears after reboot.
22. Kernel panic after update.
23. Filesystem becomes read-only.
24. NFS mount is unavailable.
25. LVM extension fails.
26. Filesystem resize fails.
27. Network interface is missing.
28. Default gateway is unreachable.
29. Duplicate IP detected.
30. High disk I/O causes application slowdown.
31. `/boot` partition is full.
32. System time is incorrect due to NTP failure.
33. Cron jobs are not executing.
34. Service starts manually but not after reboot.
35. Authentication fails due to PAM configuration.
36. Root account is locked.
37. SSH key authentication stops working.
38. SSL certificate expires in production.
39. Reverse proxy configuration is incorrect.
40. Java process consumes excessive memory.
41. OOM Killer terminates the application.
42. Process is stuck in uninterruptible sleep (D state).
43. Zombie processes continue increasing.
44. Application log directory is full.
45. Docker service fails after kernel update.
46. Backup job fails because the filesystem is full.
47. Patch rollback is required after a failed update.
48. Server hangs during shutdown.
49. Unexpected reboot occurs.
50. Perform RCA and provide preventive actions.
