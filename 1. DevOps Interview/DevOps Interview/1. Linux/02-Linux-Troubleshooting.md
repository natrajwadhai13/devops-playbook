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

---

This is the right approach. Instead of only listing topics, convert each one into an **interview question with answer**.

Use this format throughout `02-Linux-Troubleshooting.md`.

# 01. Boot Issues

---

# 📌 Jump to Questions

- Q1. Server not booting
- Q2. GRUB issues
- Q3. Kernel panic
- Q4. Emergency mode
- Q5. Rescue mode
- Q6. Initramfs prompt
- Q7. Boot loop
- Q8. Root password reset
- Q9. Boot partition full
- Q10. Missing initramfs

---

<a id="q1"></a>

# Q1. Server is not booting. How do you troubleshoot?

## Answer

If a Linux server is not booting, troubleshoot layer by layer instead of guessing.

Boot Flow:

```
BIOS/UEFI
     ↓
Boot Loader (GRUB)
     ↓
Kernel
     ↓
initramfs
     ↓
systemd/init
     ↓
Services
     ↓
Login Prompt
```

---

## Troubleshooting Steps

### Step 1

Check console messages.

Identify where the boot process stops.

---

### Step 2

Check whether GRUB menu appears.

If GRUB is missing, bootloader may be corrupted.

---

### Step 3

Check Kernel loading.

Possible errors:

- Kernel Panic
- Missing initramfs
- Missing kernel image

---

### Step 4

Boot into Rescue Mode.

```
rescue.target
```

or

```
Emergency Mode
```

---

### Step 5

Check filesystem.

```
fsck
```

---

### Step 6

Check disk usage.

```
df -h
```

```
df -i
```

---

### Step 7

Check logs.

```
journalctl -xb
```

```
dmesg
```

---

### Step 8

Check fstab.

```
cat /etc/fstab
```

Wrong UUID is a common cause.

---

### Step 9

Check boot partition.

```
df -h /boot
```

---

### Step 10

Repair GRUB if required.

---

## Commands

```bash
journalctl -xb

dmesg

fsck

cat /etc/fstab

lsblk

blkid

grub2-install

grub2-mkconfig

uname -r
```

---

## Common Causes

- Corrupted GRUB
- Corrupted filesystem
- Wrong fstab entry
- Full /boot partition
- Missing initramfs
- Kernel panic
- Disk failure
- Hardware issue

---

## Interview Tip

Always explain troubleshooting in order.

Do **not** randomly run commands.

Interviewers expect a structured approach.

---

<a id="q2"></a>

# Q2. What are GRUB issues and how do you fix them?

## Answer

GRUB (Grand Unified Bootloader) loads the Linux kernel during boot.

If GRUB is corrupted, Linux cannot start.

---

## Common GRUB Issues

- grub> prompt
- grub rescue>
- Missing Boot Loader
- Wrong boot entry
- Corrupted configuration
- Wrong UUID
- Deleted kernel

---

## Check Configuration

```bash
cat /boot/grub2/grub.cfg
```

---

## Rebuild GRUB

RHEL 8/9

```bash
grub2-mkconfig -o /boot/grub2/grub.cfg
```

Install GRUB again

```bash
grub2-install /dev/sda
```

---

## Common Interview Question

Difference between

```
grub>
```

and

```
grub rescue>
```

---

<a id="q3"></a>

# Q3. What is Kernel Panic?

## Answer

Kernel Panic is a fatal kernel error where Linux cannot continue operating safely.

The system immediately stops to prevent data corruption.

---

## Causes

- Corrupted kernel
- Bad RAM
- Wrong kernel module
- Driver issue
- Filesystem corruption
- Missing initramfs

---

## Troubleshooting

```
journalctl -k
```

```
dmesg
```

```
uname -r
```

Boot previous kernel from GRUB.

---

<a id="q4"></a>

# Q4. What is Emergency Mode?

## Answer

Emergency Mode is the minimum environment used to repair the operating system.

Only the root filesystem is mounted.

Networking is usually unavailable.

---

## Common Causes

- Wrong fstab
- Filesystem corruption
- Missing disk
- Failed mount

---

## Commands

```bash
journalctl -xb

mount

fsck

cat /etc/fstab
```

---

<a id="q5"></a>

# Q5. What is Rescue Mode?

## Answer

Rescue Mode loads additional services compared to Emergency Mode.

Networking may be available.

It is mainly used for repairing the operating system.

---

## Uses

- Repair filesystem
- Reset password
- Reinstall GRUB
- Recover data

---

<a id="q6"></a>

# Q6. What is an Initramfs Prompt?

## Answer

Initramfs is a temporary root filesystem loaded into RAM before the actual root filesystem.

If Linux cannot mount the real root filesystem, it drops to an initramfs shell.

Example:

```
dracut:/#
```

---

## Common Causes

- Missing root filesystem
- Wrong UUID
- Missing storage driver
- Corrupted initramfs

---

## Useful Commands

```bash
lsblk

blkid

lvm

exit
```

---

<a id="q7"></a>

# Q7. What causes a Boot Loop?

## Answer

A Boot Loop occurs when the system continuously reboots before completing startup.

---

## Possible Causes

- Kernel panic
- Driver issue
- Failed update
- Corrupted filesystem
- Faulty hardware

---

## Investigation

```
journalctl -xb
```

```
last reboot
```

---

<a id="q8"></a>

# Q8. How do you reset the root password?

## Answer

Boot into GRUB.

Edit kernel parameters.

Append

```
rd.break
```

or

```
init=/bin/bash
```

Remount filesystem.

```
mount -o remount,rw /
```

Reset password.

```
passwd
```

Relabel SELinux.

```
touch /.autorelabel
```

Reboot.

---

<a id="q9"></a>

# Q9. What happens if the /boot partition becomes full?

## Answer

The system may fail to install new kernels or boot correctly.

---

## Check

```bash
df -h /boot
```

List installed kernels.

```bash
rpm -q kernel
```

Remove old kernels.

```bash
dnf remove kernel-<old-version>
```

---

<a id="q10"></a>

# Q10. What happens if initramfs is missing?

## Answer

Linux cannot mount the root filesystem and fails to boot.

---

## Rebuild initramfs

RHEL 8/9

```bash
dracut --force
```

Verify

```bash
ls /boot/initramfs*
```

---

# ⭐ Interview Tips

Always troubleshoot in this order:

1. BIOS/UEFI
2. GRUB
3. Kernel
4. initramfs
5. Filesystem
6. systemd
7. Services
8. Application

This structured approach is expected in 5–10 years Linux Administrator interviews.

=======================

This is exactly the format you should follow. Below is a **GitHub-ready** section for **02. Login Issues**.

# 02. Login Issues

---

# 📌 Jump to Questions

- Q11. Unable to login
- Q12. Root login disabled
- Q13. SSH key authentication failure
- Q14. Password expired
- Q15. Account locked
- Q16. PAM issues
- Q17. Home directory missing
- Q18. Incorrect shell
- Q19. SELinux login denial
- Q20. Authentication failures

---

<a id="q11"></a>

# Q11. User is unable to login. How do you troubleshoot?

## Answer

If a user cannot log in, troubleshoot step by step instead of assuming the cause.

---

## Troubleshooting Steps

### Step 1: Check user exists

```bash
id username
```

or

```bash
getent passwd username
```

---

### Step 2: Check account status

```bash
passwd -S username
```

Example Output

```text
username PS 2025-06-01 0 99999 7 -1
```

- P = Password set
- L = Locked account

---

### Step 3: Check account expiry

```bash
chage -l username
```

---

### Step 4: Verify login shell

```bash
grep username /etc/passwd
```

Incorrect shell example:

```text
/sbin/nologin
```

or

```text
/bin/false
```

---

### Step 5: Check home directory

```bash
ls -ld /home/username
```

---

### Step 6: Check authentication logs

RHEL

```bash
tail -50 /var/log/secure
```

Ubuntu

```bash
tail -50 /var/log/auth.log
```

---

### Step 7: Check SSH service

```bash
systemctl status sshd
```

---

### Step 8: Check SELinux

```bash
getenforce
```

---

## Common Causes

- Wrong password
- Account locked
- Password expired
- Home directory missing
- Wrong shell
- SSH service stopped
- SELinux denial
- PAM configuration issue

---

## Interview Tip

Always troubleshoot in this order:

User → Password → Account → Shell → Home Directory → SSH → Logs → SELinux

---

<a id="q12"></a>

# Q12. Why is Root login disabled?

## Answer

For security reasons, direct root login is usually disabled to prevent brute-force attacks.

Instead of logging in as root, users should log in with a normal account and use:

```bash
sudo
```

or

```bash
su -
```

---

## Check Configuration

```bash
grep PermitRootLogin /etc/ssh/sshd_config
```

Possible values:

```text
PermitRootLogin no
PermitRootLogin prohibit-password
PermitRootLogin yes
```

Restart SSH

```bash
systemctl restart sshd
```

---

## Best Practice

✔ Disable direct root login.

✔ Use sudo for administrative tasks.

---

<a id="q13"></a>

# Q13. SSH key authentication is failing. How do you troubleshoot?

## Answer

Verify the following:

### 1. Public key exists

```bash
ls ~/.ssh/authorized_keys
```

---

### 2. Correct permissions

```bash
chmod 700 ~/.ssh

chmod 600 ~/.ssh/authorized_keys
```

---

### 3. Ownership

```bash
chown -R username:username ~/.ssh
```

---

### 4. SSH configuration

```bash
grep PubkeyAuthentication /etc/ssh/sshd_config
```

Should be

```text
PubkeyAuthentication yes
```

---

### 5. Check logs

```bash
tail -f /var/log/secure
```

---

## Common Causes

- Wrong permissions
- Incorrect ownership
- Missing public key
- SSH service configuration
- Wrong private key

---

<a id="q14"></a>

# Q14. What happens when a password expires?

## Answer

After password expiration, users cannot authenticate until the password is changed.

---

## Check Password Expiry

```bash
chage -l username
```

---

## Force Password Change

```bash
passwd username
```

---

## Set Expiry

```bash
chage -M 90 username
```

---

## Interview Tip

Know the difference between:

- Password Expired
- Account Expired

---

<a id="q15"></a>

# Q15. How do you unlock a locked user account?

## Answer

Check status:

```bash
passwd -S username
```

If locked:

```bash
passwd -u username
```

or

```bash
usermod -U username
```

---

## Lock Account

```bash
passwd -l username
```

---

## Common Causes

- Too many failed login attempts
- Administrator action
- Security policy

---

<a id="q16"></a>

# Q16. What are PAM issues?

## Answer

PAM (Pluggable Authentication Modules) controls authentication, account management, password policies, and session management.

Incorrect PAM configuration may prevent users from logging in.

---

## Configuration Files

```text
/etc/pam.d/
```

---

## Check Logs

```bash
tail -f /var/log/secure
```

---

## Common Issues

- Incorrect PAM configuration
- Missing PAM module
- Password policy failure
- Account restrictions

---

<a id="q17"></a>

# Q17. What happens if the user's home directory is missing?

## Answer

The user may log in successfully but encounter errors such as:

```text
Could not chdir to home directory
```

or

```text
No such file or directory
```

---

## Verify

```bash
grep username /etc/passwd
```

```bash
ls -ld /home/username
```

---

## Create Home Directory

```bash
mkdir /home/username

chown username:username /home/username
```

---

<a id="q18"></a>

# Q18. How does an incorrect shell prevent login?

## Answer

If the login shell is:

```text
/sbin/nologin
```

or

```text
/bin/false
```

the user cannot log in interactively.

---

## Verify

```bash
grep username /etc/passwd
```

---

## Change Shell

```bash
usermod -s /bin/bash username
```

---

## Valid Shells

```bash
cat /etc/shells
```

---

<a id="q19"></a>

# Q19. How do you troubleshoot SELinux login denial?

## Answer

SELinux may block user login if file contexts or policies are incorrect.

---

## Check Mode

```bash
getenforce
```

---

## Check Logs

```bash
ausearch -m AVC

journalctl -xe
```

---

## Restore Context

```bash
restorecon -Rv /home/username
```

---

## Temporary Test

```bash
setenforce 0
```

> **Note:** Use only for troubleshooting. Re-enable SELinux after testing.

---

<a id="q20"></a>

# Q20. How do you troubleshoot authentication failures?

## Answer

Authentication failures may occur due to incorrect credentials, SSH configuration, PAM issues, account policies, or SELinux.

---

## Check Logs

RHEL

```bash
tail -100 /var/log/secure
```

Ubuntu

```bash
tail -100 /var/log/auth.log
```

---

## Verify

```bash
id username

passwd -S username

chage -l username

systemctl status sshd

getenforce
```

---

## Common Causes

- Wrong password
- Locked account
- Expired password
- Expired account
- Incorrect shell
- Missing home directory
- SSH configuration issue
- PAM failure
- SELinux denial
- Firewall blocking SSH

---

# ⭐ Interview Tips

When a user cannot log in, always troubleshoot in this order:

1. Verify user exists
2. Check password status
3. Check account expiry
4. Verify login shell
5. Verify home directory
6. Check SSH service
7. Review authentication logs
8. Check PAM configuration
9. Check SELinux
10. Verify firewall/network connectivity

Following this structured approach demonstrates real production troubleshooting skills and is what interviewers typically expect from experienced Linux administrators.

==================

Perfect. Continue with the same professional format.

---

# 03. SSH Issues

---

# 📌 Jump to Questions

- Q21. SSH service not running
- Q22. Connection refused
- Q23. Connection timed out
- Q24. Permission denied (publickey)
- Q25. Host key verification failed
- Q26. SSH hangs after login
- Q27. SSH slow response
- Q28. MaxSessions reached
- Q29. SSH port changed
- Q30. Firewall blocking SSH

---

<a id="q21"></a>

# Q21. SSH service is not running. How do you troubleshoot?

## Answer

SSH service (sshd) must be running to allow remote connections.

---

## Check Service Status

```bash
systemctl status sshd
```

---

## Start Service

```bash
systemctl start sshd
```

---

## Enable at Boot

```bash
systemctl enable sshd
```

---

## Restart Service

```bash
systemctl restart sshd
```

---

## Verify Listening Port

```bash
ss -tulpn | grep ssh
```

or

```bash
netstat -tulpn | grep ssh
```

---

## Check Logs

```bash
journalctl -u sshd
```

```bash
tail -f /var/log/secure
```

---

## Common Causes

- sshd service stopped
- Configuration error
- Port conflict
- Package removed
- Firewall blocking

---

## Interview Tip

Always validate sshd configuration before restarting.

```bash
sshd -t
```

---

<a id="q22"></a>

# Q22. What does "Connection refused" mean?

## Answer

Connection refused means the server is reachable, but no service is listening on the requested port.

---

## Check

```bash
systemctl status sshd
```

```bash
ss -tulpn | grep :22
```

```bash
firewall-cmd --list-ports
```

---

## Common Causes

- sshd stopped
- Wrong port
- Firewall
- TCP Wrappers (older systems)

---

<a id="q23"></a>

# Q23. What does "Connection timed out" mean?

## Answer

The client cannot reach the server.

Usually a network problem.

---

## Check

Client

```bash
ping server-ip
```

```bash
traceroute server-ip
```

```bash
telnet server-ip 22
```

or

```bash
nc -zv server-ip 22
```

Server

```bash
ip addr
```

```bash
ip route
```

---

## Common Causes

- Firewall
- Network issue
- Wrong routing
- Security Group (Cloud)
- Server powered off

---

<a id="q24"></a>

# Q24. How do you troubleshoot "Permission denied (publickey)"?

## Answer

SSH key authentication failed.

---

## Verify

```bash
ls -la ~/.ssh
```

---

Check permissions

```bash
chmod 700 ~/.ssh

chmod 600 ~/.ssh/authorized_keys
```

---

Check ownership

```bash
chown -R user:user ~/.ssh
```

---

Check configuration

```bash
grep PubkeyAuthentication /etc/ssh/sshd_config
```

Should be

```text
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
```

---

## Check Logs

```bash
tail -f /var/log/secure
```

---

<a id="q25"></a>

# Q25. What is "Host key verification failed"?

## Answer

The client's stored SSH host key does not match the server's current host key.

This usually happens after:

- Server rebuild
- Host replacement
- SSH host key regeneration

---

## Remove Old Key

```bash
ssh-keygen -R server-ip
```

or edit

```text
~/.ssh/known_hosts
```

---

## Verify Fingerprint

```bash
ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key.pub
```

---

<a id="q26"></a>

# Q26. SSH login hangs after authentication. How do you troubleshoot?

## Answer

Authentication succeeds, but the login session never completes.

---

## Check

```bash
df -h
```

```bash
df -i
```

```bash
ls -ld /home/user
```

---

Check DNS

```bash
cat /etc/resolv.conf
```

---

Check SSH configuration

```bash
grep UseDNS /etc/ssh/sshd_config
```

Recommended

```text
UseDNS no
```

---

## Common Causes

- DNS lookup delay
- Full filesystem
- Missing home directory
- Incorrect shell
- NFS-mounted home directory unavailable

---

<a id="q27"></a>

# Q27. Why is SSH login slow?

## Answer

Slow SSH login is commonly caused by DNS resolution or GSSAPI authentication delays.

---

## Check

```bash
grep UseDNS /etc/ssh/sshd_config
```

```bash
grep GSSAPIAuthentication /etc/ssh/sshd_config
```

Recommended

```text
UseDNS no

GSSAPIAuthentication no
```

Restart

```bash
systemctl restart sshd
```

---

## Other Causes

- LDAP delay
- Kerberos
- Slow DNS
- High CPU
- High Load Average

---

<a id="q28"></a>

# Q28. What happens when MaxSessions is reached?

## Answer

SSH limits the number of simultaneous sessions.

---

## Check

```bash
grep MaxSessions /etc/ssh/sshd_config
```

Example

```text
MaxSessions 10
```

---

Restart

```bash
systemctl restart sshd
```

---

## Related Parameters

```text
MaxStartups

ClientAliveInterval

ClientAliveCountMax
```

---

<a id="q29"></a>

# Q29. SSH port has been changed. How do you verify?

## Answer

The default SSH port is **22**, but many organizations change it for security.

---

## Check Configuration

```bash
grep Port /etc/ssh/sshd_config
```

Example

```text
Port 2222
```

---

Verify Listening Port

```bash
ss -tulpn | grep ssh
```

---

Connect

```bash
ssh -p 2222 user@server
```

---

Update Firewall

```bash
firewall-cmd --add-port=2222/tcp --permanent

firewall-cmd --reload
```

---

<a id="q30"></a>

# Q30. Firewall is blocking SSH. How do you troubleshoot?

## Answer

If SSH service is running but users still cannot connect, check the firewall.

---

## Verify Firewall

```bash
systemctl status firewalld
```

---

Check Allowed Services

```bash
firewall-cmd --list-services
```

---

Check Allowed Ports

```bash
firewall-cmd --list-ports
```

---

Allow SSH

```bash
firewall-cmd --add-service=ssh --permanent

firewall-cmd --reload
```

Or allow a custom port

```bash
firewall-cmd --add-port=2222/tcp --permanent

firewall-cmd --reload
```

---

## Verify Connectivity

Client

```bash
nc -zv server-ip 22
```

or

```bash
telnet server-ip 22
```

---

# ⭐ Top Interview Question

### Interviewer:

> "SSH is not working. How will you troubleshoot?"

### Best Answer

Follow this sequence:

1. Verify server is reachable (`ping`)
2. Check SSH service (`systemctl status sshd`)
3. Verify listening port (`ss -tulpn`)
4. Validate SSH configuration (`sshd -t`)
5. Check firewall (`firewall-cmd`)
6. Verify SELinux (`getenforce`)
7. Review logs (`journalctl -u sshd`, `/var/log/secure`)
8. Check authentication (password/SSH keys)
9. Verify disk space (`df -h`, `df -i`)
10. Test connectivity (`nc`, `telnet`, `ssh -vvv`)

---

# Common Commands

```bash
systemctl status sshd
systemctl restart sshd
systemctl enable sshd
sshd -t
ss -tulpn | grep ssh
firewall-cmd --list-services
firewall-cmd --list-ports
journalctl -u sshd
tail -f /var/log/secure
ssh -vvv user@host
ssh-keygen -R host
getenforce
df -h
df -i
```

======================

Excellent. This section is **one of the most frequently asked** in Linux Administrator interviews (5–10 years).

---

# 04. Network Issues

---

# 📌 Jump to Questions

- Q31. Network unreachable
- Q32. No internet connection
- Q33. Network Interface (NIC) is down
- Q34. Wrong default gateway
- Q35. Wrong subnet mask
- Q36. Duplicate IP address
- Q37. Network interface not coming up
- Q38. MTU issues
- Q39. VLAN issues
- Q40. Bonding issues

---

<a id="q31"></a>

# Q31. How do you troubleshoot "Network is unreachable"?

## Answer

"Network is unreachable" means the system cannot find a valid route to the destination network.

---

## Troubleshooting Steps

### Step 1: Verify IP Address

```bash
ip addr show
```

---

### Step 2: Verify Routing Table

```bash
ip route
```

or

```bash
route -n
```

---

### Step 3: Check Default Gateway

```bash
ip route | grep default
```

---

### Step 4: Test Gateway Connectivity

```bash
ping <gateway-ip>
```

---

### Step 5: Check Interface Status

```bash
ip link
```

---

## Common Causes

- Wrong gateway
- Missing default route
- Interface down
- Incorrect subnet
- Firewall issue

---

## Interview Tip

Always check in this order:

1. IP Address
2. Interface Status
3. Gateway
4. Routing Table
5. Firewall

---

<a id="q32"></a>

# Q32. Server has no Internet connection. How do you troubleshoot?

## Answer

Verify network connectivity layer by layer.

---

## Check IP

```bash
ip addr
```

---

## Check Gateway

```bash
ip route
```

---

## Test Gateway

```bash
ping <gateway-ip>
```

---

## Test External IP

```bash
ping 8.8.8.8
```

If successful, network is working.

---

## Test DNS

```bash
ping google.com
```

If IP works but hostname fails, the issue is DNS.

---

## Verify DNS Configuration

```bash
cat /etc/resolv.conf
```

---

## Common Causes

- DNS issue
- Wrong gateway
- Firewall
- ISP problem
- Interface down

---

<a id="q33"></a>

# Q33. Network Interface (NIC) is down. How do you troubleshoot?

## Answer

A down interface cannot send or receive packets.

---

## Check Interface

```bash
ip link
```

Example:

```text
ens160: DOWN
```

---

## Bring Interface Up

```bash
ip link set ens160 up
```

or

```bash
nmcli connection up ens160
```

---

## Verify

```bash
ip addr
```

---

## Common Causes

- Cable unplugged
- VM adapter disconnected
- Driver issue
- Configuration error

---

<a id="q34"></a>

# Q34. How do you troubleshoot an incorrect default gateway?

## Answer

The default gateway routes traffic outside the local network.

---

## Check Gateway

```bash
ip route
```

Example:

```text
default via 192.168.1.1
```

---

## Add Default Gateway

```bash
ip route add default via 192.168.1.1
```

---

## Persistent Configuration

Using NetworkManager:

```bash
nmcli connection modify ens160 ipv4.gateway 192.168.1.1
```

---

## Verify

```bash
ip route
```

---

<a id="q35"></a>

# Q35. What problems occur due to an incorrect subnet mask?

## Answer

An incorrect subnet mask prevents communication with hosts that should be reachable.

---

## Verify

```bash
ip addr
```

Example:

```text
192.168.1.10/24
```

---

## Symptoms

- Cannot reach gateway
- Cannot communicate with nearby hosts
- Routing issues

---

## Correct Configuration

```bash
nmcli connection modify ens160 ipv4.addresses 192.168.1.10/24
```

---

<a id="q36"></a>

# Q36. How do you identify a duplicate IP address?

## Answer

A duplicate IP occurs when two devices use the same IP address.

---

## Symptoms

- Intermittent connectivity
- ARP conflicts
- Packet loss

---

## Verify

```bash
arping -D -I ens160 192.168.1.10
```

---

## Check ARP Table

```bash
ip neigh
```

---

## Common Causes

- Static IP conflict
- DHCP conflict

---

<a id="q37"></a>

# Q37. Network interface is configured but not coming up. How do you troubleshoot?

## Answer

Verify configuration, driver, and NetworkManager.

---

## Check Interface

```bash
ip link
```

---

## Check NetworkManager

```bash
nmcli device status
```

---

## Check Driver

```bash
lspci | grep Ethernet
```

---

## Review Logs

```bash
journalctl -u NetworkManager
```

---

## Restart NetworkManager

```bash
systemctl restart NetworkManager
```

---

<a id="q38"></a>

# Q38. What are MTU issues?

## Answer

MTU (Maximum Transmission Unit) defines the maximum packet size.

A mismatch can cause packet drops or slow communication.

---

## Check MTU

```bash
ip link show
```

Example:

```text
mtu 1500
```

---

## Change MTU

```bash
ip link set ens160 mtu 9000
```

---

## Test MTU

```bash
ping -M do -s 1472 8.8.8.8
```

---

## Common Symptoms

- Slow SSH
- VPN problems
- Packet fragmentation

---

<a id="q39"></a>

# Q39. How do you troubleshoot VLAN issues?

## Answer

Verify VLAN configuration and interface tagging.

---

## Show VLAN Interfaces

```bash
ip -d link
```

---

## Verify VLAN

```bash
nmcli connection show
```

---

## Check Switch Configuration

Confirm the switch port is configured for the correct VLAN.

---

## Common Causes

- Wrong VLAN ID
- Incorrect switch port configuration
- Missing VLAN interface

---

<a id="q40"></a>

# Q40. What is NIC Bonding? How do you troubleshoot bonding issues?

## Answer

NIC Bonding combines multiple network interfaces into a single logical interface for redundancy or load balancing.

---

## Check Bond Status

```bash
cat /proc/net/bonding/bond0
```

---

## Verify Interface

```bash
ip addr
```

---

## Check Active Slave

```bash
cat /proc/net/bonding/bond0
```

---

## Common Bonding Modes

- Mode 0 – Balance RR
- Mode 1 – Active Backup
- Mode 2 – XOR
- Mode 4 – 802.3ad (LACP)

---

## Common Problems

- One slave down
- Switch not configured for LACP
- Cable failure
- Driver mismatch

---

# ⭐ Top Interview Question

### Interviewer:

> "Server has no network connectivity. How do you troubleshoot?"

### Best Answer

Follow this sequence:

1. Check Interface (`ip link`)
2. Check IP Address (`ip addr`)
3. Verify Default Gateway (`ip route`)
4. Ping Gateway
5. Ping External IP (`8.8.8.8`)
6. Verify DNS (`/etc/resolv.conf`)
7. Check Firewall
8. Check Routing Table
9. Check NetworkManager
10. Review Logs

---

# Common Commands

```bash
ip addr
ip link
ip route
route -n
nmcli
ping
arping
ip neigh
ethtool
ss
netstat -rn
traceroute
mtr
journalctl -u NetworkManager
systemctl restart NetworkManager
cat /etc/resolv.conf
cat /etc/hosts
```
