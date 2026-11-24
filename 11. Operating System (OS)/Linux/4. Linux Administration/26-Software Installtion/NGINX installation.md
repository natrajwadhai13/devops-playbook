---
title: • NGINX installation
parent: 26. Software Installtion
grand_parent: • Linux Administration
grand_grand_parent: • Linux
grand_grand_grand_parent: 11. Operating System (OS)
nav_order: 3
has_children: true
---

 install **NGINX** on your Amazon EC2 machine. The steps depend a bit on which OS your EC2 is running (Amazon Linux, Ubuntu, or RHEL).

---

### 🔹 Check your EC2 OS

Run:

```bash
cat /etc/os-release
```

---

### ✅ For Amazon Linux 2023 / Amazon Linux 2

```bash
sudo dnf update -y        # Amazon Linux 2023
# or
sudo yum update -y        # Amazon Linux 2

sudo dnf install -y nginx # AL2023
# or
sudo amazon-linux-extras enable nginx1
sudo yum install -y nginx # AL2

sudo systemctl start nginx
sudo systemctl enable nginx
```

Check status:

```bash
systemctl status nginx
```

---

### ✅ For Ubuntu (20.04/22.04)

```bash
sudo apt update -y
sudo apt install -y nginx

sudo systemctl start nginx
sudo systemctl enable nginx
```

---

### ✅ For RHEL / CentOS

```bash
sudo yum update -y
sudo yum install -y nginx

sudo systemctl start nginx
sudo systemctl enable nginx
```

---

### 🔹 Verify Installation

Open in browser:

```
http://<EC2_PUBLIC_IP>
```

You should see the **Nginx Welcome Page** 🎉

---

