---
title: • Kuberntes installation
parent: 26. Software Installtion
grand_parent: • Linux Administration
grand_grand_parent: • Linux
grand_grand_grand_parent: 11. Operating System (OS)
nav_order: 2
has_children: true
---



## ☸️ Kubernetes CLI (kubectl)

### ✅ Download kubectl
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
```

### ✅ Verify checksum
```bash
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
```

### ✅ Install kubectl
```bash
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

### ✅ Check kubectl version
```bash
kubectl version --client
kubectl version
```

---

## 📦 Kind (Kubernetes IN Docker)

### ✅ Download Kind binary
```bash
# For AMD64
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.30.0/kind-linux-amd64

# For ARM64
[ $(uname -m) = aarch64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.30.0/kind-linux-arm64
```

### ✅ Install Kind
```bash
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
```

### ✅ Create and verify cluster
```bash
kind create cluster
kind get clusters
kind version
```

-----------------------------------------------


## 🚀 Minikube Installation on Ubuntu (Docker Driver)

### ✅ 1. Install Docker (if not already installed)
```bash
sudo apt update
sudo apt install docker.io -y
sudo usermod -aG docker $USER
newgrp docker
```

Verify:
```bash
docker --version
```

---

### ✅ 2. Install Minikube
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb
sudo dpkg -i minikube_latest_amd64.deb
```

Verify:
```bash
minikube version
```

---

### ✅ 3. Start Minikube with Docker driver
```bash
minikube start --driver=docker
```

⚠️ **Requires at least 2 CPUs** — if you're on a 1 vCPU EC2 instance, Minikube will fail. Use Kind instead or resize your instance.

---

### ✅ 4. Verify Kubernetes cluster
```bash
kubectl get nodes
kubectl get po -A
```

---




