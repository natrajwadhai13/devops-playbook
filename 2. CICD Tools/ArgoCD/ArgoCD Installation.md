---
title: ArgoCD Installation
parent: ArgoCD
grand_parent: 2. CICD Tools
nav_order: 1
has_children: true
---

Getting Started ArgoCD Installation- https://argo-cd.readthedocs.io/en/stable/getting_started/

**Requirements**
* Installed kubectl command-line tool.
* Have a kubeconfig file (default location is ~/.kube/config).
* CoreDNS. Can be enabled for microk8s by microk8s enable dns && microk8s stop && microk8s start

* in Centos VM need Docker kubernets and minikube kind etc 

**Install Argo CD**

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

```bash
#kubectl get pods -n argocd -w
#kubectl get svc
#kubectl get svc -n argocd

For access web browuser " type: ClusterIP" to "type: NodePort" used below command 

#kubectl edit svc argocd-server -n argocd

# minikube service list -n argocd
create tunel for iP address for access 

[natraj@localhost root]$ minikube service argocd-server -n argocd
|-----------|---------------|-------------|---------------------------|
| NAMESPACE |     NAME      | TARGET PORT |            URL            |
|-----------|---------------|-------------|---------------------------|
| argocd    | argocd-server | http/80     | http://192.168.49.2:30388 |
|           |               | https/443   | http://192.168.49.2:31385 |
|-----------|---------------|-------------|---------------------------|
[argocd argocd-server http/80
https/443 http://192.168.49.2:30388
http://192.168.49.2:31385]

---
===========================================================================

setup is: **Windows 11 → VMware → Ubuntu VM**. Inside Ubuntu VM you want to install **Docker, Kubernetes (Minikube), and ArgoCD**, and then access ArgoCD from your **Windows 11 browser**.

Here’s the **step-by-step process** tailored for that setup:

---

## **1. Prepare Ubuntu VM**

Open a terminal inside your Ubuntu VM and update:

```bash
sudo apt update && sudo apt upgrade -y
```

Install some base tools:

```bash
sudo apt install -y curl wget apt-transport-https ca-certificates gnupg lsb-release
```

---

## **2. Install Docker**

```bash
# Remove old versions if any
sudo apt remove docker docker-engine docker.io containerd runc -y

# Setup repo
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io

# Add your user to docker group
sudo usermod -aG docker $USER
newgrp docker
```

Test:

```bash
docker run hello-world
```

---

## **3. Install kubectl**

```bash
curl -LO "https://dl.k8s.io/release/$(curl -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
kubectl version --client
```

---

## **4. Install Minikube**

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
minikube version
```

Start cluster using Docker driver:

```bash
minikube start --driver=docker --cpus=4 --memory=6144
kubectl get nodes
```

---

## **5. Install Argo CD**

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Wait until all pods are running
kubectl get pods -n argocd -w
```

---

## **6. Expose Argo CD to Windows Browser**

You have **two options**:

### Option A: Port-forward (simplest)

Run inside Ubuntu:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Then on **Windows 11 browser**, go to:
👉 [https://localhost:8080](https://localhost:8080)
(You’ll need to accept the self-signed cert warning.)

⚠️ But this works **only inside VM browser**, unless you expose VM’s port.

---

### Option B: Access from Windows via VM’s IP

1. Find Ubuntu VM’s IP:

   ```bash
   ip addr show
   ```

   (Look for something like `192.168.1.xx`)

2. Expose Argo CD via NodePort:

   ```bash
   kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'
   kubectl get svc -n argocd argocd-server
   ```

   You’ll see a `NodePort` like `32xxx`.

3. On Windows browser, open:
   👉 `https://<Ubuntu_VM_IP>:<NodePort>`

---

## **7. Get Argo CD Admin Password**

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

Login with:

* **Username**: `admin`
* **Password**: (output from above)

---

✅ At this point, ArgoCD is running inside Ubuntu VM and accessible from your Windows 11 browser.

---

👉 Do you want me to make this **one single script** (bash script for Ubuntu VM) so you can just copy-paste and it installs Docker + Minikube + ArgoCD automatically?






```