---
title: • Docker installation
parent: • Software Installtion
grand_parent: • Linux
grand_grand_parent: 11. Operating System (OS)
nav_order: 1
has_children: true
---

👍 **Amazon Linux 2023 (AL2023)**

### ✅ Steps to Install Docker on Amazon Linux 2023

1. **Update system packages**

   ```bash
   sudo dnf update -y or sudo apt update
   ```

2. **Install Docker**

   ```bash
   sudo dnf install -y docker
   ```

for ubuntu

sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

````

3. **Start and Enable Docker Service**

```bash
sudo systemctl start docker
sudo systemctl enable docker
````

4. **Check Docker Version**

   ```bash
   docker --version
   docker ps
   ```

5. **Add EC2 User to Docker Group (optional, so you don’t need `sudo`)**

   ```bash
   sudo usermod -aG docker ec2-user

   sudo usermod -aG docker $USER
   newgrp docker

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

##########################################

# install **Docker Engine** on Ubuntu 24.04.2:

---

## 🔧 Step-by-Step Installation

### 1. Update System

```bash
sudo apt update
sudo apt upgrade -y
```

### 2. Install Prerequisites

```bash
sudo apt install ca-certificates curl gnupg lsb-release -y
```

### 3. Add Docker’s Official GPG Key

```bash
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

### 4. Set Up Docker Repository

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### 5. Install Docker Engine

```bash
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```

### 6. Verify Installation

```bash
sudo docker --version
sudo docker run hello-world
```

---

## ⚙️ Optional: Run Docker Without `sudo`

```bash
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
```

Now you can run `docker ps` without `sudo`.

=====================================

---

# install images Jenkins + Nexus + SonarQube + Terraform + Ansible

## 🐳 Dockerized Services (Jenkins, Nexus, SonarQube)

### 1. Jenkins

```bash
docker run -d \
  --name jenkins \
  -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts
```

The password is stored inside the container at

```bash
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

### 2. Nexus Repository

```bash
docker run -d \
  --name nexus \
  -p 8081:8081 \
  sonatype/nexus3
```

- Password: stored inside the container at:

```bash

user name is "admin"

docker exec nexus cat /nexus-data/admin.password
```

### 3. SonarQube

```bash
docker run -d \
  --name sonarqube \
  -p 9000:9000 \
  sonarqube:latest
```

````bash
- Username: admin
- Password: admin
```



👉 These three will run as containers, each mapped to a port:

- Jenkins → `http://<VM-IP>:8080`
- Nexus → `http://<VM-IP>:8081`
- SonarQube → `http://<VM-IP>:9000`

---

## ⚙️ CLI Tools (Terraform, Ansible)

### 4. Terraform

```bash
sudo apt update
sudo apt install wget unzip -y
wget https://releases.hashicorp.com/terraform/1.9.0/terraform_1.9.0_linux_amd64.zip
unzip terraform_1.9.0_linux_amd64.zip
sudo mv terraform /usr/local/bin/
terraform -version
````

### 5. Ansible

```bash
sudo apt update
sudo apt install ansible -y
ansible --version
```

---
