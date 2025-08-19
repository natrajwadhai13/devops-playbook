---
title: • ArgoCD Installation
parent: • ArgoCD
grand_parent: 2. CICD Tools
nav_order: 1
has_children: true
---
- [ArgoCD Documentation](https://argo-cd.readthedocs.io/en/stable/getting_started/)


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
```

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

## **3. Install kubectl** use officeial site

```bash
curl -LO "https://dl.k8s.io/release/$(curl -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
or 
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
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

Start cluster using Docker driver: memory less than system 

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

or used below command 

# kubectl get secret -n argocd
# kubectl edit  secret argocd-initial-admin-secret -n argocd
after run above comand we got incoripted password. like this "TDJTdUxNNXlpbUlIS3luVg=="
used below command 
# echo TDJTdUxNNXlpbUlIS3luVg== | base64 --decode

```

Login with:

* **Username**: `admin`
* **Password**: (output from above)

---

✅ At this point, ArgoCD is running inside Ubuntu VM and accessible from your Windows 11 browser.

---

Perfect 👌 let’s write down **your step-by-step notes** from the point where ArgoCD browser access was failing, all the way until it started working.

---

# ✅ Steps to Access ArgoCD in Windows Browser (Ubuntu VM on VMware + Minikube)

### 1. Checked ArgoCD Service

```bash
kubectl get svc -n argocd argocd-server
```

* It was showing `LoadBalancer` with **ClusterIP only**, no real external IP.
* So direct access from Windows browser was **not possible**.

---

### 2. Verified Service URL with Minikube

```bash
minikube service argocd-server -n argocd --url
```

* It returned URLs like:

  ```
  http://192.168.49.2:32422
  http://192.168.49.2:30996
  ```
* These worked **inside the VM only**, not from Windows.

---

### 3. Tried Direct VM IP from Windows

* Example: `https://192.168.16.136:30996/` → **not working**.
* Because Minikube’s network (192.168.49.x) is internal to VM, not bridged to Windows host.

---

### 4. Used Port Forwarding to Expose ArgoCD

Ran:

```bash
kubectl port-forward -n argocd svc/argocd-server --address 0.0.0.0 8080:443
```

* This mapped VM’s `8080` → ArgoCD service’s `443`.
* `--address 0.0.0.0` allowed **external access** (not only localhost).

---

### 5. Accessed in Windows Browser

* Opened:

  ```
  https://<VM-IP>:8080
  ```

  Example: `https://192.168.16.136:8080`
* Successfully loaded the **ArgoCD UI** 🎉

---

### 6. (Optional) Ran Port Forward in Background

For persistence:

```bash
nohup kubectl port-forward -n argocd svc/argocd-server --address 0.0.0.0 8080:443 > argo-portforward.log 2>&1 &
```

Or created a **systemd service** to auto-start on reboot.

---

📌 **Final Working Setup:**

* Ubuntu VM runs Minikube & ArgoCD.
* `kubectl port-forward` exposes service to Windows browser.
* Access URL → `https://<VM-IP>:8080`.

---

- [argocd-example-apps](https://github.com/argoproj/argocd-example-apps)

* Step : - click create Applicatio --> my-first-app (Application Name) --> Project name ( Defult) -> Sync Policy ( Automatic) --> Repository URL (https://github.com/argoproj/argocd-example-apps) --> path --> DESTINATION --> Cluster URL --> Namespace (default) --> then Create 

```bash
kubectl get deploy
```

Download Argo CD CLI:- Download the latest Argo CD version from https://github.com/argoproj/argo-cd/releases/latest. More detailed installation instructions can be found via the CLI installation documentation.

Also available in Mac, Linux and WSL Homebrew: 

```bash
brew install argocd
```

- [argocd Command Reference] (https://argo-cd.readthedocs.io/en/stable/user-guide/commands/argocd/)


---

## 1 ArgoCD CLI Installation In Ubuntu Server

```bash
wget https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
rm argocd-linux-amd64
```

---

## 2. Login to ArgoCD

1. Get initial admin password:

   ```bash
   kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
   ```

2. Login:

   ```bash
   argocd login localhost:8080 --username admin --password <PASTE_PASSWORD> --insecure
   ```

---

## 3. Deploy Example Application (Guestbook)

1. Create app:

   ```bash
   argocd app create guestbook \
     --repo https://github.com/argoproj/argocd-example-apps.git \
     --path guestbook \
     --dest-namespace default \
     --dest-server https://kubernetes.default.svc
   ```

2. Sync app:

   ```bash
   argocd app sync guestbook
   ```

3. Verify:

   ```bash
   argocd app get guestbook
   ```

---

## 6. Access Guestbook UI

1. Check service:

   ```bash
   kubectl get svc -n default
   ```

   Example:

   ```
   guestbook-ui   NodePort   10.x.x.x   <none>   80:32423/TCP   1m
   ```

2. Get minikube IP:

   ```bash
   minikube ip
   ```

3. Access in Windows browser:

   ```
   http://<minikube-ip>:<NodePort>

   curl http://<minikube-ip>:<NodePort>
   ```

---