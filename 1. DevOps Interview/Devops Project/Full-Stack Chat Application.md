---
title: "Full-Stack Chat Application"
parent: "• Devops Project"
grand_parent: "1. DevOps"
nav_order: 1
---


---

# **🚀 Kubernetes Project: Full-Stack Chat Application (3-Tier Architecture on Minikube)**

**Tech Stack:** React (Frontend), Node.js (Backend), MongoDB (Database)

**Kubernetes Objects:** Namespace, Deployment, Service, Ingress, PV, PVC, Secrets

**Tools:** Docker, Minikube, kubectl, VS Code, Docker Hub


---

# **1️⃣ Project Overview (High-Level Architecture)**

### **3-Tier Architecture**

| Tier         | Technology | Kubernetes Components                     |
| ------------ | ---------- | ----------------------------------------- |
| **Frontend** | React      | Deployment, Pod, Service, Ingress         |
| **Backend**  | Node.js    | Deployment, Pod, Service, Ingress, Secret |
| **Database** | MongoDB    | Deployment, Service, PV, PVC              |

### **Flow**

Frontend → Backend API (/api) → MongoDB

---

### **IMP Command **

```bash
# kubectl create -f namespace.yml
# kubectl get ns
# kubectl apply -f backend-deployment.yml 
# kubectl get pods -n chat-app
# kubectl apply -f frontend-deployment.yml  
# kubectl get pods -n chat-app
# kubectl describe pod backend-deployment-598bb7556f-vlplv -n chat-app
# kubectl apply -f mongodb-pv.yml
# kubectl get pv -n chat-app
# kubectl apply -f mongodb-pvc.yml
# kubectl get pv -n chat-app
# kubectl apply -f mongodb-deployment.yml
# kubectl get pods -n chat-app
# watch kubectl get pods -n chat-app
# kubectl apply -f backend-service.yml
# kubectl apply -f frontend-service.yml
# kubectl apply -f mongodb-service.yml
# kubectl apply -f secrets.yml
# kubectl apply -f ingress.yml
# kubectl get ing -n chat-app
# minikube addons enable ingress
# kubectl get ns
# kubectl get svc -n ingress-nginx
# kubectl port-forward svc/ingress-nginx-controller -n ingress-nginx 80:80 &
# kubectl get svc -n chat-app
# kubectl port-forward service/backend -n chat-app 5001:5001 & 
# kubectl port-forward service/frontend -n chat-app 80:80 &
 permition issue 
# sudo -E kubectl port-forward service/frontend -n chat-app 80:80 &

rename images

# docker tag natrajwadhai13/chatapp-fronted:latest natrajwadhai13/chatapp-frontend:latest
# docker push natrajwadhai13/chatapp-frontend:latest

delete all

# kubectl delete -f .
# kubectl create -f namespace.yml
# kubectl apply -f mongodb-pv.yml
# kubectl apply -f mongodb-pvc.yml
# kubectl apply -f .
# kubectl port-forward svc/ingress-nginx-controller -n ingress-nginx 80:80
# kubectl delete -f frontend-deployment.yml
# kubectl delete -f backend-deployment.yml
```
---

kubectl get ns

# **2️⃣ Prerequisites**

### **Install Minikube & Docker**

```bash
choco install minikube
minikube start --driver=docker
kubectl get nodes
```

### **Clone Source Code**

```bash
git clone https://github.com/natrajwadhai13/full-stack_chatApp.git
cd full-stack_chatApp
```

---

# **3️⃣ Docker Build & Push**

### **Login to Docker Hub**

```bash
docker login -u natrajwadhai13
```

### **Backend Image**

```bash
docker build -t natrajwadhai13/chatapp-backend:latest .
docker push natrajwadhai13/chatapp-backend:latest
```

### **Frontend Image**

```bash
docker build -t natrajwadhai13/chatapp-frontend:latest .
docker push natrajwadhai13/chatapp-frontend:latest
```

---

# **4️⃣ Kubernetes Deployment**

---

# **🟦 Step 1: Namespace**

`namespace.yml`

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: chat-app
```

Apply:

```bash
kubectl apply -f namespace.yml

```

---

# **🟪 Step 2: MongoDB — PV, PVC, Deployment, Service**

### **Persistent Volume**

`mongodb-pv.yml`

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mongodb-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /data
```

### **Persistent Volume Claim**

`mongodb-pvc.yml`

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mongodb-pvc
  namespace: chat-app
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

### **MongoDB Deployment**

`mongodb-deployment.yml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongodb-deployment
  namespace: chat-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels: 
        app: mongodb
    spec:
      containers:
      - name: mongodb
        image: mongo:latest
        ports:
        - containerPort: 27017
        env:
        - name: MONGO_INITDB_ROOT_USERNAME
          value: mongoadmin
        - name: MONGO_INITDB_ROOT_PASSWORD
          value: secret
        volumeMounts:
        - name: mongo-data
          mountPath: /data/db
      volumes:
      - name: mongo-data
        persistentVolumeClaim:
          claimName: mongodb-pvc
```

### **MongoDB Service**

`mongodb-service.yml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mongodb
  namespace: chat-app
spec:
  selector:
    app: mongodb
  ports:
  - port: 27017
    targetPort: 27017
```

---

# **🟦 Step 3: Secrets**

`secrets.yml`

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: chatapp-secrets
  namespace: chat-app
type: Opaque
data:
  jwt: <BASE64_JWT_SECRET>
```

Apply:

```bash
kubectl apply -f secrets.yml
```

---

# **🟧 Step 4: Backend Deployment + Service**

`backend-deployment.yml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend-deployment
  namespace: chat-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels: 
        app: backend
    spec:
      containers:
      - name: chatapp-backend
        image: natrajwadhai13/chatapp-backend:latest
        ports:
        - containerPort: 5001
        env:
        - name: NODE_ENV
          value: production
        - name: MONGODB_URI
          value: "mongodb://mongoadmin:secret@mongodb:27017/dbname?authSource=admin"
        - name: JWT_SECRET
          valueFrom:
            secretKeyRef:
              name: chatapp-secrets
              key: jwt
        - name: PORT
          value: "5001"
```

`backend-service.yml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend
  namespace: chat-app
spec:
  selector:
    app: backend
  ports:
  - port: 5001
    targetPort: 5001
```

---

# **🟩 Step 5: Frontend Deployment + Service**

`frontend-deployment.yml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend-deployment
  namespace: chat-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels: 
        app: frontend
    spec:
      containers:
      - name: chatapp-frontend
        image: natrajwadhai13/chatapp-frontend:latest
        ports:
        - containerPort: 80
```

`frontend-service.yml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: frontend
  namespace: chat-app
spec:
  selector:
    app: frontend
  ports:
  - port: 80
    targetPort: 80
```

---

# **🟥 Step 6: Ingress Configuration**

Enable Ingress in Minikube:

```bash
minikube addons enable ingress
```

`ingress.yml`

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: chatapp-ingress
  namespace: chat-app
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: chat-tws.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: frontend
            port:
              number: 80
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: backend
            port:
              number: 5001
```

Port-forward NGINX:

```bash
kubectl port-forward svc/ingress-nginx-controller -n ingress-nginx 80:80
```

Add host entry:

```
127.0.0.1 chat-tws.com
```

Visit:

```
http://chat-tws.com/
```

---

## **5️⃣ Important Theory to Speak in Interview**

### **Why We Use Namespace?**

* Logical isolation
* Easy resource management
* Separation between environments (dev, staging)

### **Why PV & PVC?**

* PV = Storage provisioned by admin
* PVC = Storage requested by application
* MongoDB needs persistent storage for database files

### **Why Ingress?**

* For HTTP routing
* Single entry point
* Path-based routing (frontend → backend)

### **Why Secrets?**

* Store sensitive values (JWT, passwords)
* Base64 encoded

### **Why Separate Services?**

* Backend Service → stable endpoint
* MongoDB Service → DNS name inside cluster
* Frontend Service → exposed using ingress

### **Deployment Benefits**

* Auto-healing
* Replica management
* Rolling updates

---

# **6️⃣ Extra Points to Impress Interviewers**

### **✔️ CI/CD Integration (Optional to speak)**

* GitHub Actions workflow → build → push → deploy to k8s

### **✔️ Can Deploy Same Project on:**

* EKS
* GKE
* AKS

### **✔️ Add Horizontal Pod Autoscaler**

* Based on CPU/Memory

### **✔️ Add ConfigMaps for environment configs**

---

# **7️⃣ Cleanup Commands**

```bash
kubectl delete -f .
kubectl delete namespace chat-app
```

================================================



# ✅ **2–3 Minute Project Explanation (Template)**



### **1. Project Overview (30 sec)**

* What was the application?
* What problem were you solving?
* What was your role?

### **2. Architecture Summary (45 sec)**

Explain the main technologies:

* Kubernetes cluster (EKS/GKE/AKS/On-Prem)
* CI/CD pipeline
* Observability & logging
* Security
* Cloud services used

### **3. Key Responsibilities (45 sec)**

Mention 4–5 bullet points:

* Deployments / Helm / YAML
* Monitoring / alerts
* Scaling & optimization
* Automation / scripting
* Troubleshooting

### **4. Achievements / Outcomes (30 sec)**

Show measurable results:

* Reduced downtime
* Faster deployments
* Improved performance
* Cost optimization

Total = 2–3 minutes.

---

# ✅ **Strong Example Answer for You (DevOps + Kubernetes)**

Use this exact wording in interviews:

---

### **1. Project Overview**

"I worked on a microservices-based application deployed on AWS EKS. The main goal was to containerize all services, automate deployments, and improve system reliability. My role as a DevOps Engineer was to manage Kubernetes workloads, implement CI/CD pipelines, and ensure smooth production operations."

---

### **2. Architecture Summary**

"The application was running on **AWS EKS**, with workloads deployed using **Helm charts**.
We used **Nginx Ingress** for routing, **Cluster Autoscaler & HPA** for scaling, and **AWS EFS & EBS** for persistent storage.

For CI/CD, we used **GitHub Actions + Argo CD** for GitOps-based deployments.
Monitoring was implemented using **Prometheus, Grafana, ELK stack and CloudWatch**.
Security included **IAM roles for service accounts, Kubernetes RBAC, Secrets encryption, Image scanning, and Network Policies**."

---

### **3. Key Responsibilities**

* "Wrote and maintained Kubernetes manifests (Deployments, Services, ConfigMaps, Secrets, Ingress)."
* "Created Helm charts for multiple microservices."
* "Implemented CI/CD pipelines that automated build, test, and deployment steps."
* "Enabled auto-scaling using HPA, VPA, and Cluster Autoscaler."
* "Configured Istio service mesh for traffic routing, mTLS, and observability."
* "Integrated Prometheus, Grafana, and Loki for logs, metrics, and alerts."
* "Troubleshot production issues using kubectl, logs, events, and dashboard tools."

---

### **4. Outcomes**

* "Deployment time reduced from 45 minutes to 5 minutes using GitOps."
* "Improved reliability with auto-healing, autoscaling, and health checks."
* "Enhanced security with RBAC, IAM, image scanning, and secrets encryption."
* "Reduced cloud cost by ~20% using right-sizing and autoscaling."


