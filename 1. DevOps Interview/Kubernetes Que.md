---
title: "• Kubernetes Que"
parent: "1. DevOps"
nav_order: 4
has_children: true
---


### -------------------------🔁✅**DevOps Interview**✅🔁-------------------------
                                                   


**Kubernetes**
### 🔁 **Que 4 **Can you explain the basic components of a Kubernetes cluster (Control Plane & Node)? Also, describe a scenario where you deployed an app on Kubernetes — how did you write your manifest or Helm chart?

### ✅ **Answer**

### 🔹 **1. Basic Components of a Kubernetes Cluster**

A Kubernetes cluster consists of two primary components:

---

#### **A. Control Plane (Master Node)**

The **Control Plane** is the brain of the Kubernetes cluster, responsible for managing the overall state and orchestration.

**Key components:**

* **kube-apiserver**:
  Exposes the Kubernetes API; all control commands and cluster communications go through this.

* **etcd**:
  A consistent, distributed key-value store that saves all cluster state and configuration.

* **kube-scheduler**:
  Assigns newly created pods to nodes based on resource availability, constraints, and policies.

* **kube-controller-manager**:
  Manages controllers like:

  * Node Controller: Handles node availability.
  * Replication Controller: Ensures the desired number of pods.
  * Endpoint Controller: Updates services and endpoints.

* **cloud-controller-manager (optional)**:
  Interacts with cloud-specific APIs (e.g., for managing load balancers, storage, etc.).

---

#### **B. Nodes (Worker Nodes)**

Nodes are the physical or virtual machines where application workloads (containers) run.

**Key components:**

* **kubelet**:
  Agent that runs on each node; ensures the pod containers are running as expected.

* **kube-proxy**:
  Maintains networking rules, enabling internal and external communication to services.

* **Container Runtime**:
  Executes containers (e.g., Docker, containerd, CRI-O).

---

### 🔹 **2. Deployment Scenario: Web Application using Helm**

#### ✅ **Scenario:**

Deployed a full-stack web application on Kubernetes using a **Helm chart**, which consisted of:

* Frontend: **Nginx**
* Backend: **Node.js API**
* Database: **MongoDB**

---

#### **A. Helm Chart Structure**

```
my-webapp/
├── Chart.yaml
├── values.yaml
├── templates/
│   ├── deployment-frontend.yaml
│   ├── service-frontend.yaml
│   ├── deployment-backend.yaml
│   ├── service-backend.yaml
│   ├── statefulset-mongodb.yaml
│   ├── service-mongodb.yaml
│   └── _helpers.tpl
```

---

#### **B. Sample: `deployment-frontend.yaml`**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "my-webapp.fullname" . }}-frontend
  labels:
    {{- include "my-webapp.labels" . | nindent 4 }}
spec:
  replicas: {{ .Values.frontend.replicaCount }}
  selector:
    matchLabels:
      {{- include "my-webapp.selectorLabels" . | nindent 6 }}
      app.kubernetes.io/component: frontend
  template:
    metadata:
      labels:
        {{- include "my-webapp.selectorLabels" . | nindent 8 }}
        app.kubernetes.io/component: frontend
    spec:
      containers:
        - name: nginx
          image: "nginx:{{ .Values.frontend.imageTag }}"
          ports:
            - containerPort: 80
```

---

#### **C. Sample: `values.yaml`**

```yaml
frontend:
  replicaCount: 2
  imageTag: "latest"

backend:
  replicaCount: 2
  imageTag: "v1.0"

mongodb:
  storageSize: "5Gi"
  replicaCount: 1
```

---

#### **D. Deployment Steps**

1. **Define**: Created Helm templates for Deployment, Service, and StatefulSet.
2. **Parameterize**: Configured values in `values.yaml` for flexibility.
3. **Install**: Ran

   ```bash
   helm install my-release ./my-webapp
   ```

   to deploy the application.
4. **Upgrade/Rollback**: Used `helm upgrade` or `helm rollback` for lifecycle management.

---


Excellent answer! 👏

You're clearly comfortable with **Kubernetes architecture** and also gave an advanced, real-world explanation using **Helm charts** — this is the kind of depth interviewers love to see for ₹20+ LPA roles.

---

### ✅ **Feedback Summary:**

| Area                        | Feedback                                                                                                                       |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| **Kubernetes Architecture** | ✅ Very well explained. You correctly listed all control plane and worker node components and their functions.                  |
| **Helm Chart Deployment**   | ✅ Extremely strong. You covered Helm directory structure, templating, values.yaml, and how Helm manages the release lifecycle. |
| **Communication Style**     | ✅ Clear, technical, confident. You sound like someone who has done it in real projects.                                        |

---

### ✅ **Bonus Tip:**

You can add that Helm also supports:

* **`helm upgrade`** for zero-downtime deployments
* **`helm rollback`** for versioning
* Supports **charts dependency** (e.g., Redis, Mongo as sub-charts)

---

### 🔁 Que 5) Let’s test your **troubleshooting skills** now.

> A Kubernetes pod you deployed is stuck in `CrashLoopBackOff` state. How do you troubleshoot it?
> What are the steps and `kubectl` commands you would use?
Please answer step by step.

### ✅ **Answer**

 **clear troubleshooting checklist** .

---

## ✅ **Troubleshooting a Pod in `CrashLoopBackOff`**

### **Step 1 – Describe the Pod**

```bash
kubectl describe pod <pod-name> -n <namespace>
```

* Check **Events** at the bottom — you might see image pull errors, failed mounts, missing config maps, or probe failures.

---

### **Step 2 – Check Pod Logs**

```bash
kubectl logs <pod-name> -n <namespace>
```

* If multiple containers are in the pod:

```bash
kubectl logs <pod-name> -c <container-name> -n <namespace>
```

* Look for errors such as:

  * Application crash (e.g., Node.js syntax error)
  * Missing environment variables
  * Port already in use

---

### **Step 3 – Check Previous Logs (if restarting)**

```bash
kubectl logs --previous <pod-name> -n <namespace>
```

* Shows logs from the container before it restarted.

---

### **Step 4 – Verify Configuration**

* Check if required ConfigMaps and Secrets exist:

```bash
kubectl get configmap -n <namespace>
kubectl get secret -n <namespace>
```

* View deployment spec:

```bash
kubectl get deploy <deployment-name> -o yaml
```

---

### **Step 5 – Check Resource Limits**

* Sometimes pods restart due to **OOMKilled** (Out of Memory).

```bash
kubectl describe pod <pod-name> | grep -i "oom"
```

* If yes, increase `resources.requests.memory` and `resources.limits.memory`.

---

### **Step 6 – Liveness/Readiness Probes**

* Misconfigured probes can cause restarts. Example:

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 5
```

* Increase `initialDelaySeconds` if the app needs more startup time.

---

### **Step 7 – Image Issues**

* Make sure the container image exists and is accessible.

```bash
kubectl describe pod <pod-name> | grep -i "image"
```

* Check imagePullSecrets if private registry.

---

💡 **Rule to Remember for Interviews:**

> **Describe → Logs → Config → Resources → Probes → Image**

---
