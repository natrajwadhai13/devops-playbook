---
title: Docker installation
parent: • Software Installtion
grand_parent: • Linux
grand_grand_parent: 11. Operating System (OS)
nav_order: 2
has_children: true
---

👍 **Amazon Linux 2023 (AL2023)**, so Docker installation is slightly different than Amazon Linux 2 (where we used `amazon-linux-extras`).

Here’s how you can install **Docker on Amazon Linux 2023**:

---

### ✅ Steps to Install Docker on Amazon Linux 2023

1. **Update system packages**

   ```bash
   sudo dnf update -y
   ```

2. **Install Docker**

   ```bash
   sudo dnf install -y docker
   ```

3. **Start and Enable Docker Service**

   ```bash
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

4. **Check Docker Version**

   ```bash
   docker --version
   ```

5. **Add EC2 User to Docker Group (optional, so you don’t need `sudo`)**

   ```bash
   sudo usermod -aG docker ec2-user
   ```

   Then log out and back in:

   ```bash
   exit
   ssh -i mykey.pem ec2-user@<EC2_PUBLIC_IP>
   ```

6. **Test Docker**

   ```bash
   docker run hello-world
   ```

   This should download a test image and run it.

---

### ✅ If You Also Need Docker Compose

Amazon Linux 2023 doesn’t bundle Docker Compose, so install it manually:

```bash
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

---
