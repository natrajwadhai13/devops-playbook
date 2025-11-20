---
title: "• Ingress & Volume & Storage Class"
parent: "• Kubernetes-Basics"
grand_parent: "• Kubernetes"
great_grand_parent: 3. DevOps Core Tools
nav_order: 2
has_children: true
---


# 📌 Kubernetes Storage & Networking & Ingress

## 🔷 1. Persistent Volume (PV)

A **Persistent Volume (PV)** is a piece of storage in the cluster that is **provisioned by an admin** or **dynamically provisioned** through a StorageClass.
It is a **cluster-level** storage resource, independent of pods.

### ⭐ Key Points

* PV is a **physical storage** unit in Kubernetes.
* Lifecycle is independent of Pods.
* Supports storage types like EBS, NFS, Ceph, GCE Disk, Azure Disk.
* Defined by size, access modes, storage backend.
* A PV remains even if a Pod using it is deleted.

---

## 🔷 2. Persistent Volume Claim (PVC)

A **Persistent Volume Claim (PVC)** is a request from a Pod for storage.
PVC asks for storage (size, access mode), and Kubernetes binds it to an appropriate PV.

### ⭐ Key Points

* PVC is created by the user/app, PV is the actual storage.
* Pods use storage via PVC (Pods never use PV directly).
* PVC binds to a matching PV (based on size, accessMode, StorageClass).
* When PVC is deleted, behavior depends on PV reclaim policy (`Retain`, `Recycle`, `Delete`).

---

## 🔷 3. StorageClass

A **StorageClass** defines the **type of storage** and the **provisioning method** for dynamic PV creation.

### ⭐ Key Points

* Used for **dynamic provisioning** of PVs.
* Provides different storage types like *ssd*, *gp2*, *standard*, *premium*.
* Defines parameters like IOPS, filesystem, replication, etc.
* PVC referencing a StorageClass automatically creates a PV.
* Commonly used in cloud providers (AWS EBS, GCP Persistent Disk, Azure Disk).

---

## 🔥 PV vs PVC vs StorageClass (Short Summary)

* **PV →** Actual storage in cluster (supply).
* **PVC →** Request for storage (demand).
* **StorageClass →** Template that automatically creates PVs.

---



===========================================


# ✅ **KUBERNETES STORAGE + DEPLOYMENT + SERVICE + INGRESS

(Clean, Arranged, Interview-Level Notes)**

---

# 🔹 **SECTION 1 — Kubernetes Storage Concepts**

Kubernetes me storage ke 4 main components hote hai:

1️⃣ **Persistent Volume (PV)**
2️⃣ **Persistent Volume Claim (PVC)**
3️⃣ **Storage Class (SC)**
4️⃣ **Volume Mounts inside Pods**

Storage ka flow:

```
Node Storage → PV → PVC → Pod Volume Mount
```

---

# 🔹 **STEP 1 — Create Persistent Volume (PV)**

**File: persistentVolume.yml**

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-pv
  namespace: nginx
spec:
  capacity:
    storage: 2Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: local-storage
  hostPath:
    path: /mnt/data
```

### Commands:

```bash
kubectl apply -f persistentVolume.yml
kubectl get pv
```

---

# 🔹 **STEP 2 — Create Persistent Volume Claim (PVC)**

**File: persistentVolumeClaim.yml**

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: local-pvc
  namespace: nginx
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: local-storage
```

### Commands:

```bash
kubectl apply -f persistentVolumeClaim.yml
kubectl get pvc -n nginx
```

📌 **Note**: PVC claim ho jaye to PV ka status **Bound** ho jata hai.

---

# 🔹 **STEP 3 — Deploy Nginx with Volume Mount**

**File: deployment.yml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
        volumeMounts:
        - mountPath: /var/www/html
          name: my-volume
      volumes:
      - name: my-volume
        persistentVolumeClaim:
          claimName: local-pvc
```

---

### ❗ **Common Error**

```
FailedScheduling: persistentvolumeclaim "local-pvc" not found
```

### 🔧 **Solution**

Namespaces PV/PVC me add karo:

```yaml
metadata:
  namespace: nginx
```

Then delete & recreate:

```bash
kubectl delete pvc/local-pvc -n nginx
kubectl delete pv/local-pv
kubectl apply -f persistentVolume.yml
kubectl apply -f persistentVolumeClaim.yml
```

---

# 🔹 **STEP 4 — Check Data Storage Location**

Pod → Docker Container → HostPath storage

Commands:

```bash
kubectl get pods -n nginx -o wide
kubectl get nodes
```

Container me enter:

```bash
docker exec -it <container-id> bash
cd /mnt/data/
```

Yehi aapka actual saved data hota hai.

---

# 🔹 **STEP 5 — Create Nginx Service**

**File: service.yml**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  namespace: nginx
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

Apply:

```bash
kubectl apply -f service.yml
kubectl get svc -n nginx
```

---

# 🔹 **Expose Nginx using Port Forward**

```bash
kubectl port-forward service/nginx-service -n nginx 81:80 --address=0.0.0.0
```

---

# 🟦 SECTION 2 — Deploy Real Django Notes App

---

### **STEP 1: Build Docker Image**

```bash
docker build -t notes-app-k8s .
```

### **STEP 2: Login & Push to Docker Hub**

```bash
docker login -u natrajwadhai13
docker tag notes-app-k8s:latest natrajwadhai13/notes-app-k8s:latest
docker push natrajwadhai13/notes-app-k8s:latest
```

---

### **Create Namespace**

**namespace.yml**

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: notes-app
```

---

### **Create Deployment**

**deployment.yml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: notes-app-deployment
  namespace: notes-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: notes-app
  template:
    metadata:
      labels:
        app: notes-app
    spec:
      containers:
      - name: notes-app
        image: natrajwadhai13/notes-app-k8s:latest
        ports:
        - containerPort: 8000
```

---

### **Create Service**

**service.yml**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: notes-app-service
  namespace: notes-app
spec:
  selector:
    app: notes-app
  ports:
    - port: 8000
      targetPort: 8000
  type: ClusterIP
```

---

### Apply All

```bash
kubectl apply -f namespace.yml
kubectl apply -f deployment.yml
kubectl apply -f service.yml
```

### Port Forward

```bash
kubectl port-forward service/notes-app-service -n notes-app 8000:8000 --address=0.0.0.0
```

---

# 🟦 SECTION 3 — Ingress Setup 



# 🌐 Ingress Controller (Theory)

An **Ingress Controller** is a Kubernetes component that **implements Ingress rules** and acts as a **reverse proxy + load balancer** for HTTP/HTTPS traffic entering the cluster.

It listens to Ingress resources and routes external traffic to the correct Services.

---

## ⭐ Key Features

* Provides **Layer 7 load balancing** (HTTP/HTTPS).
* Allows routing based on **URL path**, **host/domain**, and **TLS termination**.
* Consolidates multiple Services under a single external IP.
* Supports SSL certificates, redirects, rewrites, rate limiting, authentication.
* Common Ingress Controllers: **NGINX**, **HAProxy**, **Traefik**, **AWS ALB Ingress**, **GKE Ingress**.

---

## 🔷 What Ingress Controller Does?

* Watches Ingress objects for changes.
* Configures the underlying proxy (e.g., NGINX).
* Routes traffic to appropriate backend service.
* Handles TLS/HTTPS.
* Acts as a single entry point for external traffic.

---

## 🔥 Ingress vs Ingress Controller (Short Summary)

* **Ingress →** Rules for routing traffic.
* **Ingress Controller →** Actual software that applies those rules (NGINX/Traefik/etc.).


---

### STEP 1 — Install Ingress Controller

```bash
kubectl apply -f https://kind.sigs.k8s.io/examples/ingress/deploy-ingress-nginx.yaml
```

Check:

```bash
kubectl get pods -n ingress-nginx
kubectl get svc -n ingress-nginx
```

---

### STEP 2 — Create Ingress Rule

**ingress.yml**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-notes-ingress
  namespace: nginx
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - pathType: Prefix
        path: /nginx
        backend:
          service:
            name: nginx-service
            port:
              number: 80
      - pathType: Prefix
        path: /
        backend:
          service:
            name: notes-app-service
            port:
              number: 8000
```

Apply:

```bash
kubectl apply -f ingress.yml
kubectl get ing -n nginx
```

---

### STEP 3 — Expose Ingress to External IP

```bash
kubectl port-forward svc/ingress-nginx-controller -n ingress-nginx 8080:80 --address=0.0.0.0
```

Access:

```
http://<server-ip>:8080/nginx
http://<server-ip>:8080/
```

---


### ✔ "I have set up complete Kubernetes storage & application deployment pipeline using:"

* PV, PVC, StorageClass
* Deployment with Volume Mount
* ClusterIP service
* Port-forward access
* Deployed Django Notes App on Kubernetes
* Pushed Docker image to private registry
* Ingress Controller + URL routing
* Worked on troubleshooting Pending Pods, PVC binding issues