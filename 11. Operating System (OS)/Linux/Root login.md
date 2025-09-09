---
title: Root login
parent: • Linux
grand_parent: 11. Operating System (OS)
nav_order: 1
has_children: true
---
### Root login 

By default, **AWS EC2 machines (Amazon Linux / Ubuntu / RHEL, etc.) do not allow root login with password** for security reasons. Instead, AWS enforces **SSH key pair authentication**.
But if you really want to **enable root password login**, you need to change both **SSH config** and **root password settings**.

⚠️ **Warning:** Allowing root password login is considered insecure. A safer alternative is to use key-based authentication or login with `ec2-user` (Amazon Linux) / `ubuntu` (Ubuntu) and then `sudo su -` to root.

---

### ✅ Steps to Allow Root Password Login on Amazon EC2

1. **Login using Key Pair**

   ```bash
   ssh -i mykey.pem ec2-user@<EC2_PUBLIC_IP>
   ```

2. **Switch to Root**

   ```bash
   sudo su -
   ```

3. **Set a Root Password**

   ```bash
   passwd root
   ```

   Enter a strong password.

4. **Edit SSH Configuration**
   Open SSH server config:

   ```bash
   vi /etc/ssh/sshd_config
   ```

   Modify or add these lines:

   ```
   PermitRootLogin yes
   PasswordAuthentication yes
   ```

   > In Amazon Linux 2023, you may also need to check `/etc/ssh/sshd_config.d/50-cloud-init.conf`.

5. **Restart SSH Service**

   ```bash
   systemctl restart sshd
   ```

6. **Update Security Group in AWS**

   * Go to **EC2 Console → Security Groups → Inbound Rules**.
   * Make sure **port 22 (SSH)** is allowed from your IP.

7. **Login as Root with Password**

   ```bash
   ssh root@<EC2_PUBLIC_IP>
   ```

   It will now ask for the root password you set.

---

