---
title: "• Azure AKS"
parent: "• Azure"
grand_parent: "4. Cloud Platforms"
nav_order: 8
has_children: true
---

👉 [GitHub Repo link for AZ-Cli]()

---

# 🧩 8. Azure Kubernetes Service (AKS) ⭐⭐⭐⭐⭐\*

**Focus:** AKS Architecture, Networking, Workloads, Storage, Scaling, Deployment, Ingress, Security, Production Operations, Troubleshooting and Interview Preparation.

## 🔗 Quick Navigation\*

- [8.1 AKS Architecture]\(#81-aks-architecture)

- [8.2 AKS Networking]\(#82-aks-networking)

- [8.3 Kubernetes Workloads]\(#83-kubernetes-workloads)

- [8.4 AKS Storage]\(#84-aks-storage)

- [8.5 AKS Scaling]\(#85-aks-scaling)

- [8.6 AKS Deployment]\(#86-aks-deployment)

- [8.7 AKS Ingress and TLS]\(#87-aks-ingress-and-tls)

- [8.8 AKS Security]\(#88-aks-security)

- [8.9 Production Deployment]\(#89-production-deployment)

- [8.10 AKS Troubleshooting]\(#810-aks-troubleshooting)

- [8.11 Production Scenarios]\(#811-production-scenarios)

- [8.12 AKS Interview Questions]\(#812-aks-interview-questions)

- [8.13 AKS Best Practices]\(#813-aks-best-practices)

- [8.14 AKS Cheat Sheet]\(#814-aks-cheat-sheet)

---

<a id="81-aks-architecture"></a>

# 8.1 AKS Architecture

## Theory

AKS is a managed Kubernetes platform. Azure operates the managed control-plane components, while the DevOps team is responsible for node pools, workloads, networking choices, identities, policies, application configuration and day-2 operations.

## Concept / Definition\*

**Azure Kubernetes Service, AKS** is Azure's managed Kubernetes service.

Azure manages the Kubernetes **control plane**, while we normally manage the **worker nodes, node pools, workloads and application configuration**.

### Basic Architecture\*

```text

Developer

   ↓

GitLab / GitHub

   ↓

CI/CD Pipeline

   ↓

Docker Image

   ↓

Azure Container Registry

   ↓

AKS

   ├── Control Plane

   │    ├── API Server

   │    ├── Scheduler

   │    ├── Controller Manager

   │    └── etcd

   │

   └── Node Pools

        ├── System Node Pool

        └── User Node Pool

             ├── Pod

             ├── Container

             └── Application

```

## Important Components\*

**Control Plane**

- API Server → Kubernetes API entry point

- Scheduler → decides where Pods run

- Controller Manager → maintains desired state

- etcd → stores Kubernetes cluster state

**Node**

- Runs application workloads

- Contains kubelet

- Contains container runtime

- Provides CPU, memory and networking

**Node Pool**

- Group of similar VM nodes

- Can have different VM sizes

- Can scale independently

**System Node Pool**

- Runs critical AKS/Kubernetes system Pods

- Should not be used heavily for application workloads

**User Node Pool**

- Intended for application workloads

- Can have different VM sizes and scaling rules

## VM Size Selection\*

Choose VM size based on:

- CPU requirement

- Memory requirement

- Pod density

- Network requirement

- Application workload

- Cost

Do not simply choose the largest VM.

## Hands-on\*

```bash

az aks create \\

  --resource-group rg-nexcart \\

  --name aks-nexcart \\

  --node-count 3 \\

  --node-vm-size Standard_D4s_v5 \\

  --generate-ssh-keys

```

Get credentials:

```bash

az aks get-credentials \\

  --resource-group rg-nexcart \\

  --name aks-nexcart

```

Check cluster:

```bash

kubectl get nodes

kubectl get pods -A

kubectl cluster-info

```

## Useful Commands\*

```bash

kubectl get nodes -o wide

kubectl describe node \<node>

kubectl get nodes --show-labels

kubectl top nodes

kubectl get namespaces

```

## Production Use Case\*

For NexCart:

```text

System Pool

    ↓

AKS system components


User Pool

    ↓

product-service

order-service

payment-service

notification-service

```

Keep application workloads mainly on user pools.

---

<a id="82-aks-networking"></a>

# 8.2 AKS Networking

## Theory

AKS networking has three practical layers: Azure VNet connectivity, Kubernetes Service-to-Pod connectivity, and north-south traffic through Ingress or another Layer-7 entry point. Production design should provide predictable IP planning, internal DNS, controlled Pod-to-Pod traffic and secure access to Azure dependencies.

## Concept / Definition\*

AKS networking controls how:

```text

User → Ingress → Service → Pod

```

and:

```text

Pod → Service → External Dependency

```

communicate.

## Network Models\*

### Azure CNI\*

Pods receive IP connectivity from Azure networking.

Common modern production choice:

```text

VNet

 ├── AKS Subnet

 │    ├── Node

 │    ├── Pod

 │    └── Pod

```

### Azure CNI Overlay\*

Pods use a separate pod CIDR while nodes use VNet IPs.

Benefits:

- Conserves VNet IP addresses

- Better for large clusters

- Simplifies IP planning

**Recommended for new production designs in many cases.**

### Kubenet\*

Traditional Kubernetes networking model.

Important:

**Kubenet is scheduled for AKS retirement on March 31, 2028. Plan new deployments around Azure CNI Overlay instead.**

## Kubernetes Service Types\*

### ClusterIP\*

Internal service communication.

```yaml
apiVersion: v1

kind: Service

metadata:

  name: product-service

spec:

  type: ClusterIP

  selector:

    app: product

  ports:

    - port: 80

      targetPort: 8082
```

### LoadBalancer\*

Creates an Azure load balancer for external access.

```yaml
spec:

  type: LoadBalancer
```

### NodePort\*

Exposes service through a node port.

Usually avoid direct NodePort exposure in production when Ingress is available.

## DNS\*

Inside Kubernetes:

```text

service.namespace.svc.cluster.local

```

Example:

```text

product-service.nexcart.svc.cluster.local

```

Check DNS:

```bash

kubectl exec -it \<pod> -- nslookup product-service

```

## Network Policies\*

Network Policies control Pod-to-Pod traffic.

Example concept:

```text

frontend

   ↓ allowed

backend

   ↓ allowed

database


frontend

   X

database

```

## Production Use Case\*

NexCart:

```text

Internet

   ↓

Ingress

   ↓

frontend

   ↓

product-service

   ↓

database

```

Only required communication should be allowed.

---

<a id="83-kubernetes-workloads"></a>

# 8.3 Kubernetes Workloads

## Theory

Kubernetes continuously tries to make actual state match desired state. Deployments are normally used for stateless microservices, ReplicaSets maintain Pod count, and Pods are disposable execution units. Configuration and secrets should be separated from container images.

## Pod\*

Smallest deployable Kubernetes unit.

```text

Pod

 └── Container

      └── Application

```

Pods are disposable.

Never treat a Pod like a permanent server.

## Deployment\*

Manages stateless application Pods.

```yaml
apiVersion: apps/v1

kind: Deployment

metadata:

  name: product-service

spec:

  replicas: 3

  selector:

    matchLabels:

      app: product

  template:

    metadata:

      labels:

        app: product

    spec:

      containers:

        - name: product

          image: myacr.azurecr.io/product-service:1.0

          ports:

            - containerPort: 8082
```

## ReplicaSet\*

Ensures the requested number of Pods are running.

Normally Deployment manages ReplicaSets for us.

```text

Deployment

    ↓

ReplicaSet

    ↓

Pods

```

## StatefulSet\*

Use for stateful workloads where stable identity/storage is required.

Examples:

- Database

- Kafka

- Stateful application

Do not automatically use StatefulSet just because an application has data. Prefer managed Azure database services when suitable.

## ConfigMap\*

Stores non-sensitive configuration.

```yaml
apiVersion: v1

kind: ConfigMap

metadata:

  name: product-config

data:

  APP_ENV: production

  PORT: "8082"
```

## Secret\*

Used for sensitive Kubernetes configuration.

However, for production Azure workloads, prefer:

```text

Azure Key Vault

      ↓

Managed Identity / Workload Identity

      ↓

AKS

```

instead of storing long-lived credentials directly in Git.

## Useful Commands\*

```bash

kubectl get pods

kubectl get pods -o wide

kubectl describe pod \<pod>

kubectl logs \<pod>

kubectl logs \<pod> --previous

kubectl exec -it \<pod> -- sh

kubectl get deploy

kubectl get rs

kubectl get svc

kubectl get configmap

kubectl get secret

```

---

<a id="84-aks-storage"></a>

# 8.4 AKS Storage

## Theory

Container filesystems are ephemeral. Persistent business data needs persistent storage plus an explicit backup and restore strategy. In AKS, PVCs provide the application-facing abstraction while CSI-backed Azure storage provides persistence.

## Concept\*

Containers are temporary.

If a Pod is deleted:

```text

Pod deleted

   ↓

Container filesystem lost

```

For persistent data:

```text

Pod

 ↓

PVC

 ↓

PV

 ↓

Azure Disk / Azure Files

```

## PV\*

**PersistentVolume** represents storage available to Kubernetes.

## PVC\*

**PersistentVolumeClaim** is the application's request for storage.

```text

Application

    ↓

PVC

    ↓

PV

    ↓

Azure Storage

```

## StorageClass\*

Defines how storage should be dynamically provisioned.

Example:

```yaml

apiVersion: v1

kind: PersistentVolumeClaim

metadata:

  name: app-data

spec:

  accessModes:

    - ReadWriteOnce

  storageClassName: managed-csi

  resources:

    requests:

      storage: 20Gi

```

## Azure Disk\*

Good for:

- Single-node attached storage

- High-performance workloads

- Stateful applications requiring block storage

Typical access:

```text

ReadWriteOnce

```

## Azure Files\*

Useful when multiple Pods/nodes need shared file storage.

```text

Pod A ─┐

Pod B ─┼── Azure Files

Pod C ─┘

```

## Production Rule\*

Do not keep important production database data only inside container filesystem.

Prefer:

- Azure SQL

- Azure Database for PostgreSQL

- Azure Database for MySQL

- Cosmos DB

- Azure Storage

- Azure Disk / Azure Files where Kubernetes storage is actually required

## Backup\*

AKS Backup can protect Kubernetes resources and supported CSI-based persistent volumes. Current Azure Backup support includes Azure Disk and Azure SMB Files scenarios, subject to service limitations.

Backup flow:

```text

AKS

 ↓

Backup Extension

 ↓

Backup Vault / Storage

 ↓

Recovery Point

```

---

<a id="85-aks-scaling"></a>

# 8.5 AKS Scaling

## Theory

Scaling has two levels: application capacity and cluster capacity. HPA changes Pod replicas, while Cluster Autoscaler changes node capacity. Good production scaling also depends on resource requests, limits, health probes and workload-specific metrics.

## HPA\*

**Horizontal Pod Autoscaler** increases/decreases Pod replicas based on metrics.

```text

CPU high

   ↓

HPA

   ↓

More Pods

```

Example:

```yaml
apiVersion: autoscaling/v2

kind: HorizontalPodAutoscaler

metadata:

  name: product-hpa

spec:

  scaleTargetRef:

    apiVersion: apps/v1

    kind: Deployment

    name: product-service

  minReplicas: 2

  maxReplicas: 10

  metrics:

    - type: Resource

      resource:

        name: cpu

        target:

          type: Utilization

          averageUtilization: 70
```

## Cluster Autoscaler\*

HPA creates more Pods.

If there is no node capacity:

```text

HPA

 ↓

More Pods

 ↓

Pods Pending

 ↓

Cluster Autoscaler

 ↓

More Nodes

```

### Important Difference\*

```text

HPA

→ scales Pods


Cluster Autoscaler

→ scales Nodes

```

## Node Scaling\*

Check:

```bash

kubectl get nodes

kubectl top nodes

kubectl get pods -o wide

```

AKS node pool:

```bash

az aks nodepool scale \\

  --resource-group rg-nexcart \\

  --cluster-name aks-nexcart \\

  --name userpool \\

  --node-count 5

```

## Production Scaling\*

Use:

```text

HPA

\+

Cluster Autoscaler

\+

Resource Requests/Limits

\+

Pod Distribution

```

Do not depend only on CPU.

For real applications, also consider:

- Memory

- Request rate

- Queue length

- Latency

- Custom metrics

---

<a id="86-aks-deployment"></a>

# 8.6 AKS Deployment

## Theory

A production deployment separates build from runtime. CI validates and packages an immutable image, ACR stores it, and CD promotes it to AKS using Helm or another deployment mechanism. Health checks determine release success and rollback must be available.

## Production Flow\*

```text

Developer

 ↓

GitLab

 ↓

CI Pipeline

 ↓

Unit Test

 ↓

Security Scan

 ↓

Docker Build

 ↓

ACR Push

 ↓

CD Pipeline

 ↓

AKS

 ↓

Helm

 ↓

Rolling Deployment

```

## ACR → AKS\*

ACR stores Docker images.

```bash

az acr login --name myacr

docker build -t myacr.azurecr.io/product-service:1.0 .

docker push myacr.azurecr.io/product-service:1.0

```

Attach ACR to AKS:

```bash

az aks update \\

  --resource-group rg-nexcart \\

  --name aks-nexcart \\

  --attach-acr myacr

```

## Helm\*

Helm packages Kubernetes resources.

Typical structure:

```text

helm/

└── product-service/

    ├── Chart.yaml

    ├── values.yaml

    └── templates/

        ├── deployment.yaml

        ├── service.yaml

        ├── ingress.yaml

        └── hpa.yaml

```

Deploy:

```bash

helm upgrade --install product-service ./helm/product-service \\

  -n nexcart \\

  --create-namespace

```

Check:

```bash

helm list -n nexcart

helm status product-service -n nexcart

```

Rollback:

```bash

helm history product-service -n nexcart


helm rollback product-service 1 -n nexcart

```

## Environment Configuration\*

Prefer:

```text

values-dev.yaml

values-qa.yaml

values-prod.yaml

```

Example:

```yaml

replicaCount: 3


image:

  repository: myacr.azurecr.io/product-service

  tag: "1.0"


resources:

  requests:

    cpu: "250m"

    memory: "256Mi"

  limits:

    cpu: "500m"

    memory: "512Mi"

```

Do not hardcode production secrets in `values.yaml`.

---

<a id="87-aks-ingress-and-tls"></a>

# 8.7 AKS Ingress and TLS

## Theory

Ingress is the HTTP/HTTPS routing layer between external clients and Kubernetes Services. Production design should define DNS, TLS termination, routing, backend health and certificate rotation.

## Ingress\*

Ingress provides HTTP/HTTPS routing.

Example:

```text

Internet

   ↓

Ingress

   ├── /api/products → product-service

   ├── /api/orders   → order-service

   └── /api/payment  → payment-service

```

## NGINX Ingress\*

Common Kubernetes ingress approach.

Example:

```yaml
apiVersion: networking.k8s.io/v1

kind: Ingress

metadata:

  name: nexcart-ingress

spec:

  ingressClassName: nginx

  rules:

    - host: nexcart.example.com

      http:

        paths:

          - path: /api/products

            pathType: Prefix

            backend:

              service:

                name: product-service

                port:

                  number: 80
```

## Application Gateway\*

Azure-native Layer 7 traffic management option.

Useful when you need:

- Azure integration

- WAF

- Centralized HTTP routing

- TLS termination

For new architecture decisions, also evaluate the current AKS application-routing/Application Gateway options rather than automatically choosing legacy ingress patterns.

## TLS\*

Typical flow:

```text

Client

 ↓ HTTPS

Ingress / Application Gateway

 ↓

Service

 ↓

Pod

```

Certificate can be managed using:

- Kubernetes TLS Secret

- Azure Key Vault integration

- Managed certificate approach where supported

## Certificate Rotation\*

Production should avoid manually updating certificates inside every Pod.

Preferred approach:

```text

Certificate Source

       ↓

Key Vault / Certificate Management

       ↓

Ingress

       ↓

HTTPS

```

---

<a id="88-aks-security"></a>

# 8.8 AKS Security

## Theory

AKS security follows least privilege and identity-based access. Kubernetes RBAC controls permissions, Workload Identity provides Azure-resource access without long-lived credentials, and Key Vault centralizes secrets. Network policies and scanning add workload-level controls.

## RBAC\*

Kubernetes RBAC controls:

```text

User

 ↓

Role / ClusterRole

 ↓

Permission

```

Example:

```yaml
apiVersion: rbac.authorization.k8s.io/v1

kind: Role

metadata:

  namespace: nexcart

  name: pod-reader

rules:

  - apiGroups: [""]

    resources: ["pods"]

    verbs: ["get", "list"]
```

## Workload Identity\*

Allows an application running in AKS to access Azure resources without storing Azure credentials inside the application.

```text

Pod

 ↓

Kubernetes ServiceAccount

 ↓

Workload Identity

 ↓

Microsoft Entra ID

 ↓

Azure Resource

```

Example use cases:

- Key Vault

- Storage

- Azure Service Bus

- Azure APIs

## Key Vault CSI Driver\*

Application can consume secrets from Azure Key Vault through the CSI driver.

Concept:

```text

AKS Pod

 ↓

CSI Driver

 ↓

Workload Identity

 ↓

Key Vault

```

This avoids putting permanent secrets into Git.

## Network Policies\*

Control which Pods can communicate.

Example:

```text

frontend → backend     ALLOW

backend → database     ALLOW

frontend → database    DENY

```

## Security Rules\*

- No secrets in Git

- No credentials inside Docker image

- Use Workload Identity

- Use Key Vault

- Least privilege RBAC

- Network segmentation

- Restrict public endpoints

- Scan container images

- Scan dependencies

- Keep AKS/Kubernetes versions supported

- Use resource requests and limits

- Use Pod Security controls appropriate to the workload

---

<a id="89-production-deployment"></a>

# 8.9 Production Deployment

## Theory

A production release is more than a successful kubectl apply. Availability depends on replicas, probes, disruption controls, scheduling rules, safe rollout settings and a tested rollback path.

## Rolling Update\*

Default safe deployment pattern.

```text

Old Pods: 3

New Pods: 1

Old Pods: 2

New Pods: 2

Old Pods: 1

New Pods: 3

```

Application remains available during deployment when correctly configured.

## Rollback\*

```bash

kubectl rollout history deployment/product-service -n nexcart


kubectl rollout undo deployment/product-service -n nexcart


kubectl rollout status deployment/product-service -n nexcart

```

## Pod Disruption Budget\*

PDB protects availability during voluntary disruptions.

```yaml
apiVersion: policy/v1

kind: PodDisruptionBudget

metadata:

  name: product-pdb

spec:

  minAvailable: 2

  selector:

    matchLabels:

      app: product
```

## Affinity\*

Controls where Pods should run.

Useful for:

- Spreading replicas

- Keeping related workloads together

- Zone awareness

## Taints and Tolerations\*

Used to control which Pods can run on specific nodes.

```text

Node

 ↓

Taint


Pod

 ↓

Matching Toleration

 ↓

Allowed

```

Example production use:

```text

GPU Node

 ↓

Taint

 ↓

Only GPU workloads allowed

```

## Readiness Probe\*

Determines whether Pod can receive traffic.

```yaml
readinessProbe:

  httpGet:

    path: /health

    port: 8082

  initialDelaySeconds: 10

  periodSeconds: 10
```

## Liveness Probe\*

Checks whether the application is alive.

```yaml
livenessProbe:

  httpGet:

    path: /health

    port: 8082

  initialDelaySeconds: 30

  periodSeconds: 20
```

## Startup Probe\*

Useful for slow-starting applications.

```text

Startup Probe

     ↓

Application starts

     ↓

Readiness Probe

     ↓

Traffic

```

---

<a id="810-aks-troubleshooting"></a>

# 8.10 AKS Troubleshooting

## Theory

Use an evidence-first sequence: scope → Pods → nodes → events → logs → Service/endpoints → Ingress/DNS → Azure resource → root cause → fix → verification → prevention.

## First-Level Commands\*

```bash

kubectl get pods -A

kubectl get nodes

kubectl get svc

kubectl get ingress

kubectl get events -A --sort-by=.lastTimestamp

```

Detailed:

```bash

kubectl describe pod \<pod> -n nexcart

kubectl logs \<pod> -n nexcart

kubectl logs \<pod> --previous -n nexcart

kubectl describe node \<node>

```

Application test:

```bash

kubectl exec -it \<pod> -n nexcart -- sh

```

Then:

```bash

curl http://product-service

nslookup product-service

```

---

## Pending Pod\*

### Check\*

```bash

kubectl get pods

kubectl describe pod \<pod>

kubectl get nodes

kubectl describe node \<node>

```

### Common Causes\*

- Not enough CPU

- Not enough memory

- Node taint

- Affinity mismatch

- PVC not available

- Resource quota

- No suitable node

### Fix\*

Check scheduler events first.

```bash

kubectl describe pod \<pod>

```

---

## CrashLoopBackOff\*

### Meaning\*

Container starts and repeatedly crashes.

### Check\*

```bash

kubectl logs \<pod>

kubectl logs \<pod> --previous

kubectl describe pod \<pod>

```

### Root Causes\*

- Application startup failure

- Wrong environment variable

- Missing Secret

- Database unavailable

- Wrong command

- Port/configuration issue

- Application bug

---

## OOMKilled\*

### Meaning\*

Container exceeded memory limit.

### Check\*

```bash

kubectl describe pod \<pod>

kubectl top pod \<pod>

```

Look for:

```text

Reason: OOMKilled

```

### Fix\*

- Check application memory usage

- Increase memory limit if justified

- Check memory leak

- Tune JVM/Node/Python application

- Set correct requests and limits

Do not blindly increase memory.

---

## ImagePullBackOff\*

### Check\*

```bash

kubectl describe pod \<pod>

```

Look at Events.

### Common Causes\*

- Wrong image name

- Wrong tag

- Image doesn't exist

- ACR authentication issue

- Network issue

- Registry permission issue

Check image:

```bash

az acr repository list --name myacr

```

---

## 404\*

Check:

```text

Client

 ↓

Ingress

 ↓

Path Rule

 ↓

Service

 ↓

Pod

```

Commands:

```bash

kubectl get ingress

kubectl describe ingress \<name>

kubectl get svc

kubectl get endpoints

```

Common causes:

- Wrong Ingress path

- Wrong service

- Wrong application route

- Wrong backend port

---

## 502 / 503\*

Check:

```bash

kubectl get pods

kubectl get svc

kubectl get endpoints

kubectl describe ingress \<ingress>

```

Possible flow:

```text

Ingress

   ↓

Service

   ↓

No healthy endpoint

   ↓

502/503

```

Check selector:

```bash

kubectl get svc product-service -o yaml

kubectl get pods --show-labels

```

Service selector must match Pod labels.

---

## Node NotReady\*

Check:

```bash

kubectl get nodes

kubectl describe node \<node>

kubectl get pods -A -o wide

```

Investigate:

- CPU

- Memory

- Disk

- Network

- Kubelet

- VM health

- Azure platform issue

---

<a id="811-production-scenarios"></a>

# 8.11 Production Scenarios

## Theory

For interviews and incidents, structure the answer as Problem → What will you check? → Commands → Evidence → Root cause → Fix → Validation → Prevention.

## Scenario 1: Pods are Pending\*

```text

Problem

→ Pods Pending


Check

→ kubectl describe pod


Check

→ CPU / Memory / Taints / Affinity / PVC


Root Cause

→ No suitable node capacity


Fix

→ Scale node pool / correct resources / remove scheduling issue


Prevention

→ HPA + Cluster Autoscaler + correct requests

```

## Scenario 2: Deployment completed but application gives 503\*

```text

Check Pod

↓

Check Service

↓

Check Endpoints

↓

Check Ingress

↓

Check Readiness Probe

```

Most common production issue:

```text

Pod running

≠

Pod ready

```

If readiness fails, Service may have no usable endpoints.

## Scenario 3: New image deployed but old version is running\*

Check:

```bash

kubectl describe deployment product-service

kubectl get pods -o jsonpath="{.items[*].spec.containers[*].image}"

```

Possible causes:

- Image tag not changed

- `imagePullPolicy`

- Deployment not updated

- Wrong namespace

- Wrong Helm values

## Scenario 4: Application suddenly becomes slow\*

Check:

```bash

kubectl top pods

kubectl top nodes

kubectl get hpa

kubectl get pods -o wide

```

Then check:

- CPU

- Memory

- Pod restarts

- Database latency

- Network

- Dependency latency

- Request rate

- HPA behavior

## Scenario 5: Node pool has no capacity\*

```text

Pending Pods

   ↓

Check Events

   ↓

Insufficient CPU/Memory

   ↓

Cluster Autoscaler

   ↓

New Node

   ↓

Pod Scheduled

```

If autoscaler does not scale:

- Check min/max

- Check node pool state

- Check VM quota

- Check subnet/IP availability

- Check scheduling constraints

## Scenario 6: Secret rotation required\*

Preferred design:

```text

Key Vault

   ↓

Workload Identity

   ↓

CSI Driver

   ↓

AKS Pod

```

Do not manually update secrets inside every Pod.

After rotation, verify whether the application needs restart/reload based on how the secret is consumed.

---

<a id="812-aks-interview-questions"></a>

# 8.12 AKS Interview Questions

## Theory

Answer AKS questions from an operations perspective: explain the architecture, state what you would check, give key commands, identify likely root causes and finish with prevention.

### Q1. What is AKS?\*

**Answer:** AKS is Azure's managed Kubernetes service where Azure manages the Kubernetes control plane and we manage workloads, node pools and application configuration.

### Q2. What is the difference between System and User node pools?\*

**Answer:** System node pools run critical Kubernetes/AKS system workloads. User node pools are intended mainly for application workloads and can be scaled independently.

### Q3. Azure CNI vs Kubenet?\*

**Answer:** Azure CNI provides Azure-network integration for Pod connectivity, while Kubenet uses a simpler Kubernetes networking model. For new AKS designs, Azure CNI Overlay is commonly preferred. Kubenet is scheduled for retirement on March 31, 2028.

### Q4. HPA vs Cluster Autoscaler?\*

**Answer:** HPA scales Pods. Cluster Autoscaler scales nodes when Pods cannot be scheduled because of insufficient capacity.

### Q5. Pod is Pending. What will you check?\*

**Answer:**

```bash

kubectl describe pod \<pod>

kubectl get nodes

kubectl describe node \<node>

```

I check scheduler events, CPU/memory, taints, affinity, PVC and resource constraints.

### Q6. Pod is CrashLoopBackOff. What will you do?\*

**Answer:**

```bash

kubectl logs \<pod>

kubectl logs \<pod> --previous

kubectl describe pod \<pod>

```

Then identify application/configuration/dependency/resource failure.

### Q7. Pod is running but application is not accessible. What will you check?\*

**Answer:**

```text

Pod

→ Readiness

→ Service

→ Endpoints

→ Ingress

→ DNS

→ Network

```

### Q8. 502 from Ingress. What do you check?\*

**Answer:** I check Ingress configuration, Service, Service endpoints, target port, Pod readiness and application logs.

### Q9. How do you rollback AKS deployment?\*

```bash

kubectl rollout history deployment/product-service

kubectl rollout undo deployment/product-service

kubectl rollout status deployment/product-service

```

With Helm:

```bash

helm history product-service -n nexcart

helm rollback product-service \<REVISION> -n nexcart

```

### Q10. How do you securely access Key Vault from AKS?\*

**Answer:** Use Microsoft Entra Workload Identity with the Azure Key Vault CSI Driver instead of storing Azure credentials inside Pods.

### Q11. How do you store persistent data in AKS?\*

**Answer:** Use Kubernetes PVC backed by Azure Disk or Azure Files when Kubernetes persistent storage is required. For business databases, prefer managed Azure database services where appropriate.

### Q12. How do you achieve HA in AKS?\*

**Answer:**

```text

Multiple replicas

\+

Multiple nodes

\+

Multiple availability zones where supported

\+

Pod anti-affinity / topology spread

\+

PDB

\+

HPA

\+

Cluster Autoscaler

\+

Reliable storage

```

### Q13. How do you deploy a new application version?\*

**Answer:**

```text

Git

→ CI

→ Test

→ Security Scan

→ Docker Build

→ ACR

→ Helm

→ AKS

→ Rolling Update

→ Health Verification

→ Rollback if required

```

### Q14. How do you troubleshoot ImagePullBackOff?\*

**Answer:** Check the Pod events, image name/tag, ACR repository, AKS-to-ACR authorization and network connectivity.

### Q15. Why do we need readiness and liveness probes?\*

**Answer:** Readiness controls whether a Pod should receive traffic. Liveness detects an unhealthy application that needs restart.

---

<a id="813-aks-best-practices"></a>

# 8.13 AKS Best Practices

## Theory

Production AKS should be designed for HA, security, controlled deployment, observability, backup/restore, predictable scaling and operational recovery.

## Architecture\*

- Use separate System and User node pools

- Use multiple replicas for production

- Use availability zones where supported and appropriate

- Use Azure CNI Overlay for new designs where suitable

- Plan IP ranges before cluster creation

- Keep production and non-production separated

## Workloads\*

- Use Deployments for stateless applications

- Use StatefulSets only when stable identity/storage is required

- Always define resource requests

- Define reasonable limits

- Use readiness/liveness/startup probes

- Use PDB for critical applications

- Use topology spread/affinity where required

## Security\*

- Use Workload Identity

- Use Key Vault

- Avoid hardcoded secrets

- Apply least-privilege RBAC

- Use Network Policies

- Scan images

- Keep Kubernetes/AKS versions supported

## Deployment\*

- Use immutable image tags

- Prefer Helm for complex applications

- Use automated CI/CD

- Use rolling/canary/blue-green strategy where required

- Keep rollback capability

- Verify health after deployment

## Storage\*

- Use managed databases where possible

- Use CSI drivers

- Do not depend on container filesystem

- Define backup and restore process

- Test restoration, not only backup

## Operations\*

- Monitor Pods and nodes

- Monitor application latency and errors

- Configure alerts

- Keep production runbooks

- Maintain incident/RCA documentation

- Test scaling

- Test rollback

- Test disaster recovery

---

<a id="814-aks-cheat-sheet"></a>

# 8.14 AKS Cheat Sheet

## Theory

Use this section for rapid interview and production revision. Remember: Cluster → Nodes → Pods → Workloads → Service → Ingress → DNS/Network → Azure dependencies, with Security, Scaling, Monitoring and Backup across the stack.

## Cluster\*

```bash

az aks list -o table


az aks show \\

  -g \<resource-group> \\

  -n \<cluster>


az aks get-credentials \\

  -g \<resource-group> \\

  -n \<cluster>

```

## Nodes\*

```bash

kubectl get nodes

kubectl describe node \<node>

kubectl top nodes

```

## Pods\*

```bash

kubectl get pods -A

kubectl get pods -o wide

kubectl describe pod \<pod>

kubectl logs \<pod>

kubectl logs \<pod> --previous

kubectl top pods

```

## Deployment\*

```bash

kubectl get deploy

kubectl describe deploy \<name>

kubectl rollout status deploy/\<name>

kubectl rollout history deploy/\<name>

kubectl rollout undo deploy/\<name>

```

## Service\*

```bash

kubectl get svc

kubectl describe svc \<name>

kubectl get endpoints

```

## Ingress\*

```bash

kubectl get ingress

kubectl describe ingress \<name>

```

## Configuration\*

```bash

kubectl get configmap

kubectl get secret

```

## Storage\*

```bash

kubectl get pv

kubectl get pvc

kubectl get storageclass

```

## Scaling\*

```bash

kubectl get hpa


kubectl scale deployment product-service \\

  --replicas=5

```

## Helm\*

```bash

helm list -A

helm status \<release>

helm history \<release>

helm upgrade --install \<release> \<chart>

helm rollback \<release> \<revision>

```

## AKS Node Pool\*

```bash

az aks nodepool list \\

  -g \<resource-group> \\

  --cluster-name \<cluster> \\

  -o table


az aks nodepool scale \\

  -g \<resource-group> \\

  --cluster-name \<cluster> \\

  -n \<nodepool> \\

  --node-count 5

```

## Troubleshooting Flow\*

```text

Issue

 ↓

kubectl get

 ↓

kubectl describe

 ↓

kubectl logs

 ↓

kubectl get events

 ↓

Check Pod

 ↓

Check Service

 ↓

Check Ingress

 ↓

Check DNS / Network

 ↓

Check Azure Resource

 ↓

Root Cause

 ↓

Fix

 ↓

Verify

 ↓

Prevent

```

## ⭐ AKS One-Line Revision\*

```text

AKS = Control Plane + Node Pools + Pods + Services + Ingress + Storage + HPA + Cluster Autoscaler + RBAC + Workload Identity + Key Vault + Helm + Monitoring + Backup + HA

```

## ⭐ NexCart AKS Architecture\*

```text

GitLab

  ↓

GitLab CI/CD

  ↓

Docker Build

  ↓

ACR

  ↓

AKS

  ├── System Node Pool

  │

  └── User Node Pool

       ├── frontend

       ├── product-service

       ├── order-service

       ├── payment-service

       └── notification-service

              ↓

       Azure Services

              ├── Key Vault

              ├── Database

              ├── Storage

              └── Monitoring

```

## ⭐ Interview Formula\*

```text

Problem

 ↓

What will you check?

 ↓

kubectl / az commands

 ↓

Logs + Events + Metrics

 ↓

Root Cause

 ↓

Fix

 ↓

Validation

 ↓

Prevention

```

**Production mindset:** Don't answer only with `kubectl` commands. Explain **what you check, why you check it, how you identify root cause, how you fix it, and how you prevent it from happening again.**
