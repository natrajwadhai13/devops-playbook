---
title: "06-azure-acr"
parent: "3. NexCard Interview"
grand_parent: "NexCart"
grand_grand_parent: "• Devops Project"
grand_grand_grand_parent: "1. DevOps"
nav_order: 6
---
# Azure Container Registry (ACR)

## 1. What is Azure Container Registry?

**Azure Container Registry (ACR)** is Azure's private container registry used to store, manage, scan, and distribute Docker/OCI images.

For NexCart:

```text
Developer
   ↓
GitLab Repository
   ↓
GitLab CI/CD
   ↓
Docker Build
   ↓
Azure Container Registry (ACR)
   ↓
AKS
   ↓
Pull Image
   ↓
Run NexCart Pods
```

ACR replaces a public registry such as Docker Hub for private enterprise workloads.

---

## 2. Why ACR is Required

Without a private registry:

```text
GitLab CI → Docker Hub → AKS
```

With Azure:

```text
GitLab CI → ACR → AKS
```

Benefits:

- Private image storage
- Azure-native integration
- Integration with AKS
- RBAC-based access
- Image versioning
- Image retention policies
- Geo-replication with supported tiers
- Private networking options
- Microsoft Defender integration
- Vulnerability/security scanning options
- OCI artifact support

---

## 3. ACR Naming

Example:

```text
ACR Name:
nexcartacr

Login Server:
nexcartacr.azurecr.io
```

Image:

```text
nexcartacr.azurecr.io/product-service:1.0.0
```

Using Git commit SHA:

```text
nexcartacr.azurecr.io/product-service:a81f92c
```

Production should preferably deploy immutable image references such as:

```text
nexcartacr.azurecr.io/product-service@sha256:<digest>
```

instead of repeatedly changing a mutable tag such as:

```text
latest
```

---

# 4. Create Azure Container Registry

```bash
az login

az account set --subscription "<subscription-id>"

az group create \
  --name nexcart-rg \
  --location centralindia

az acr create \
  --resource-group nexcart-rg \
  --name nexcartacr \
  --sku Standard
```

Verify:

```bash
az acr show \
  --resource-group nexcart-rg \
  --name nexcartacr \
  -o table
```

Get login server:

```bash
az acr show \
  --name nexcartacr \
  --query loginServer \
  -o tsv
```

Expected:

```text
nexcartacr.azurecr.io
```

---

# 5. ACR SKU

| SKU | Typical Use |
|---|---|
| Basic | Development / learning |
| Standard | Production workloads with moderate requirements |
| Premium | Enterprise workloads, private endpoints, geo-replication and advanced features |

For NexCart production architecture:

```text
Development → Basic/Standard
Production   → Standard/Premium depending on requirements
```

Do not select Premium only because it is production. Choose based on required capabilities, security, networking, replication, and scale.

---

# 6. Login to ACR

Interactive login:

```bash
az acr login --name nexcartacr
```

Verify:

```bash
docker login nexcartacr.azurecr.io
```

Azure CLI authentication is preferred over storing static registry passwords in CI/CD.

---

# 7. Build NexCart Docker Image

Example for Product Service:

```bash
cd product-service

docker build \
  -t nexcartacr.azurecr.io/product-service:1.0.0 .
```

Verify:

```bash
docker images
```

Test locally:

```bash
docker run --rm -p 8082:8082 \
  nexcartacr.azurecr.io/product-service:1.0.0
```

Test:

```bash
curl http://localhost:8082/health
```

---

# 8. Push Image to ACR

Login:

```bash
az acr login --name nexcartacr
```

Push:

```bash
docker push \
  nexcartacr.azurecr.io/product-service:1.0.0
```

Verify repository:

```bash
az acr repository list \
  --name nexcartacr \
  -o table
```

Expected:

```text
product-service
```

---

# 9. List Image Tags

```bash
az acr repository show-tags \
  --name nexcartacr \
  --repository product-service \
  -o table
```

Example:

```text
1.0.0
1.0.1
1.0.2
```

Get image manifest:

```bash
az acr repository show \
  --name nexcartacr \
  --repository product-service \
  --image 1.0.0
```

---

# 10. Recommended Image Tagging Strategy

Avoid:

```text
latest
```

Recommended:

```text
product-service:<git-sha>
```

Example:

```text
product-service:a81f92c
```

or:

```text
product-service:1.4.2
```

Best production practice:

```text
Tag:
product-service:a81f92c

Deploy:
product-service@sha256:<digest>
```

Advantages:

- Easy rollback
- Traceability
- Auditability
- No ambiguity
- Reproducible deployment

---

# 11. NexCart ACR Repositories

Example:

```text
nexcartacr.azurecr.io/product-service
nexcartacr.azurecr.io/order-service
nexcartacr.azurecr.io/payment-service
nexcartacr.azurecr.io/notification-service
```

Example images:

```text
nexcartacr.azurecr.io/product-service:a81f92c
nexcartacr.azurecr.io/order-service:b721ef3
nexcartacr.azurecr.io/payment-service:91dca21
nexcartacr.azurecr.io/notification-service:71aa321
```

---

# 12. ACR → AKS Integration

AKS needs permission to pull private images from ACR.

Preferred enterprise pattern:

```text
AKS
 ↓
Kubelet / Managed Identity
 ↓
Azure RBAC
 ↓
AcrPull
 ↓
ACR
```

Grant `AcrPull` to the AKS kubelet identity.

Get AKS identity:

```bash
az aks show \
  --resource-group nexcart-rg \
  --name nexcart-aks \
  --query identityProfile.kubeletidentity.clientId \
  -o tsv
```

Get ACR resource ID:

```bash
az acr show \
  --name nexcartacr \
  --query id \
  -o tsv
```

Assign permission:

```bash
az role assignment create \
  --assignee <kubelet-client-id> \
  --role AcrPull \
  --scope <acr-resource-id>
```

Verify:

```bash
az role assignment list \
  --assignee <kubelet-client-id> \
  --scope <acr-resource-id> \
  -o table
```

---

# 13. Important: ACR Pull vs Workload Identity

These are different authentication paths.

### AKS pulling container images

```text
AKS Node/Kubelet Identity
        ↓
AcrPull
        ↓
ACR
```

### Application accessing Azure resources

```text
Pod
 ↓
Kubernetes ServiceAccount
 ↓
Azure Workload Identity
 ↓
Managed Identity
 ↓
Key Vault / Storage / Azure API
```

Do not use a Kubernetes ServiceAccount password for ACR authentication.

Modern Kubernetes ServiceAccounts use projected short-lived tokens, and Azure Workload Identity provides a secure federation mechanism.

---

# 14. AKS Deployment Using ACR Image

Example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: product-service
spec:
  replicas: 2
  selector:
    matchLabels:
      app: product-service
  template:
    metadata:
      labels:
        app: product-service
    spec:
      containers:
        - name: product-service
          image: nexcartacr.azurecr.io/product-service:a81f92c
          ports:
            - containerPort: 8082
```

Apply:

```bash
kubectl apply -f deployment.yaml
```

Verify:

```bash
kubectl get pods -n nexcart
```

---

# 15. Verify Image Used by Pod

```bash
kubectl get pod <pod-name> \
  -n nexcart \
  -o jsonpath='{.spec.containers[*].image}'
```

Verify image ID/digest:

```bash
kubectl describe pod <pod-name> -n nexcart
```

Look for:

```text
Image:
Image ID:
```

This is useful during production troubleshooting to confirm whether the expected image was actually deployed.

---

# 16. ACR Authentication Options

### Preferred

```text
Managed Identity + Azure RBAC
```

### Application identity

```text
Workload Identity
```

### Legacy / less preferred

```text
ACR Admin User
```

### Kubernetes imagePullSecret

Can be used when managed identity integration is not available or for specific external/private registry scenarios.

Example:

```bash
kubectl create secret docker-registry acr-secret \
  --docker-server=nexcartacr.azurecr.io \
  --docker-username=<username> \
  --docker-password=<password> \
  -n nexcart
```

Then:

```yaml
spec:
  imagePullSecrets:
    - name: acr-secret
```

For AKS + ACR, prefer managed identity/RBAC instead of distributing registry credentials.

---

# 17. ACR Admin User

Check:

```bash
az acr show \
  --name nexcartacr \
  --query adminUserEnabled
```

Enable:

```bash
az acr update \
  --name nexcartacr \
  --admin-enabled true
```

Get credentials:

```bash
az acr credential show \
  --name nexcartacr
```

However, avoid using the ACR admin account for normal production CI/CD.

Why?

- Shared credential
- Broad access
- Secret rotation required
- Difficult auditability
- Higher blast radius

Prefer:

```text
Managed Identity
Azure RBAC
OIDC/Federated Identity
```

---

# 18. GitLab CI/CD → ACR

Recommended architecture:

```text
GitLab
   ↓
GitLab Runner
   ↓
OIDC / Federated Identity
   ↓
Azure
   ↓
ACR
```

Pipeline:

```text
Git Push
   ↓
Test
   ↓
Security Scan
   ↓
Docker Build
   ↓
ACR Login
   ↓
Docker Push
   ↓
Helm Deployment
   ↓
AKS
```

Example conceptual GitLab job:

```yaml
build_and_push:
  stage: build
  script:
    - az login --service-principal ...
    - az acr login --name nexcartacr
    - docker build -t nexcartacr.azurecr.io/product-service:$CI_COMMIT_SHA .
    - docker push nexcartacr.azurecr.io/product-service:$CI_COMMIT_SHA
```

For production, avoid storing long-lived Azure client secrets when OIDC/federated identity is available.

---

# 19. Build Once, Deploy Many

A strong production principle:

```text
Build
  ↓
Test
  ↓
Scan
  ↓
Push ONE immutable image
  ↓
Deploy same image to Dev
  ↓
Deploy same image to Test
  ↓
Deploy same image to Production
```

Do not rebuild the application separately for every environment.

Bad:

```text
Build Dev
Build Test
Build Prod
```

Better:

```text
Build once
   ↓
Immutable artifact
   ↓
Promote across environments
```

Environment-specific configuration should be externalized through:

```text
Helm values
ConfigMap
Key Vault
Workload Identity
Environment variables
```

---

# 20. ACR Image Retention

Over time:

```text
product-service:001
product-service:002
product-service:003
...
product-service:500
```

Storage increases and old images become difficult to manage.

Use ACR retention policies and cleanup processes.

Review repositories:

```bash
az acr repository list \
  --name nexcartacr \
  -o table
```

Check tags:

```bash
az acr repository show-tags \
  --name nexcartacr \
  --repository product-service \
  -o table
```

Before deleting an image, confirm:

- Not running in production
- Not required for rollback
- Not referenced by another environment
- No active deployment depends on it

---

# 21. Delete Old Image

Example:

```bash
az acr repository delete \
  --name nexcartacr \
  --image product-service:1.0.0
```

Use carefully.

Production cleanup should normally be automated using retention policies rather than manual deletion.

---

# 22. ACR Geo-Replication

For enterprise/global applications:

```text
        ACR
         |
   +-----+------+
   |            |
Central India  East US
   |            |
  AKS          AKS
```

Benefits:

- Regional availability
- Faster image pulls
- Reduced cross-region transfer
- Better disaster recovery

Geo-replication is a Premium ACR capability.

---

# 23. ACR Private Endpoint

For highly restricted environments:

```text
GitLab / Azure Network
        |
   Private Network
        |
   Private Endpoint
        |
       ACR
```

Public network access can be restricted depending on the architecture.

Validate:

- Private DNS
- VNet connectivity
- NSG
- Firewall
- AKS network path
- Private endpoint configuration

---

# 24. ACR Security

Production controls:

```text
RBAC
Managed Identity
OIDC
Private Endpoint
Network Restrictions
Image Scanning
Immutable Tags
Retention Policies
Audit Logs
Least Privilege
```

Do not store:

```text
ACR password
Azure client secret
Database password
```

inside:

```text
Dockerfile
Git repository
Helm values.yaml
```

Use:

```text
Azure Key Vault
GitLab protected variables
Managed Identity
Workload Identity
```

depending on the use case.

---

# 25. Image Security Flow

Recommended:

```text
Developer
   ↓
GitLab
   ↓
SAST
   ↓
Dependency Scan
   ↓
Docker Build
   ↓
Trivy/Image Scan
   ↓
Push to ACR
   ↓
Deploy to AKS
```

Example Trivy:

```bash
trivy image \
  nexcartacr.azurecr.io/product-service:a81f92c
```

Fail deployment for vulnerabilities according to the organization's defined severity policy.

Do not blindly block every vulnerability without considering:

- Severity
- Exploitability
- Runtime exposure
- Fixed version availability
- False positives
- Business risk

---

# 26. ACR Troubleshooting

## Issue 1: `unauthorized`

Example:

```text
unauthorized: authentication required
```

Check:

```bash
az login
az account show
az acr login --name nexcartacr
```

Check RBAC:

```bash
az role assignment list \
  --scope <acr-resource-id> \
  -o table
```

---

## Issue 2: AKS `ImagePullBackOff`

Check:

```bash
kubectl get pods -n nexcart
```

Then:

```bash
kubectl describe pod <pod-name> -n nexcart
```

Look for:

```text
Failed to pull image
unauthorized
repository does not exist
manifest unknown
DNS failure
network timeout
```

Check image:

```bash
az acr repository show-tags \
  --name nexcartacr \
  --repository product-service \
  -o table
```

Check AKS identity:

```bash
az aks show \
  -g nexcart-rg \
  -n nexcart-aks \
  --query identityProfile.kubeletidentity
```

Check `AcrPull`:

```bash
az role assignment list \
  --assignee <kubelet-client-id> \
  -o table
```

---

# 27. `ImagePullBackOff` Troubleshooting Flow

```text
ImagePullBackOff
      ↓
kubectl describe pod
      ↓
Check exact error
      ↓
Is image name correct?
      ↓
Does repository exist?
      ↓
Does tag exist?
      ↓
Does AKS identity have AcrPull?
      ↓
Can AKS reach ACR?
      ↓
Check private endpoint/DNS/firewall
      ↓
Restart/rollout after fixing
```

---

# 28. `manifest unknown`

Example:

```text
manifest unknown
```

Usually means the image/tag does not exist.

Check:

```bash
az acr repository show-tags \
  --name nexcartacr \
  --repository product-service \
  -o table
```

Compare with:

```yaml
image:
  repository: nexcartacr.azurecr.io/product-service
  tag: a81f92c
```

Common cause:

```text
GitLab built:
product-service:a81f92c

Helm deployed:
product-service:a81f92d
```

Fix the version mismatch.

---

# 29. `unauthorized` During AKS Pull

Check:

```bash
az role assignment list \
  --assignee <kubelet-client-id> \
  --scope <acr-resource-id>
```

Expected role:

```text
AcrPull
```

If missing:

```bash
az role assignment create \
  --assignee <kubelet-client-id> \
  --role AcrPull \
  --scope <acr-resource-id>
```

Allow time for RBAC propagation, then restart the deployment if required:

```bash
kubectl rollout restart deployment/product-service -n nexcart
```

---

# 30. ACR and AKS Network Troubleshooting

If RBAC is correct but pulling still fails:

```text
Check DNS
   ↓
Check Private Endpoint
   ↓
Check VNet
   ↓
Check NSG
   ↓
Check Firewall
   ↓
Check Public Network Access
   ↓
Check AKS outbound connectivity
```

Useful Azure checks:

```bash
az acr show \
  --name nexcartacr \
  --query "{loginServer:loginServer,publicNetworkAccess:publicNetworkAccess}"
```

For private ACR environments, validate private DNS resolution from the AKS network.

---

# 31. Verify ACR Login Server

```bash
az acr show \
  --name nexcartacr \
  --query loginServer \
  -o tsv
```

Expected:

```text
nexcartacr.azurecr.io
```

The same value must be used in Kubernetes:

```yaml
image: nexcartacr.azurecr.io/product-service:a81f92c
```

---

# 32. ACR Operational Checklist

### Before Deployment

```text
✓ Image built
✓ Unit tests passed
✓ Security scan passed
✓ Correct image tag
✓ Image pushed to ACR
✓ Image exists in ACR
✓ AKS has AcrPull
✓ Network connectivity available
✓ Helm values point to correct image
```

### After Deployment

```bash
kubectl get pods -n nexcart

kubectl rollout status \
  deployment/product-service \
  -n nexcart

kubectl describe pod <pod-name> -n nexcart
```

Verify:

```text
✓ Correct image
✓ Pod Running
✓ Ready = 1/1
✓ No ImagePullBackOff
✓ No CrashLoopBackOff
✓ Application health check passes
```

---

# 33. Production Day-2 Problems

ACR management does not stop after deployment.

Common operational problems:

| Problem | Impact | Prevention |
|---|---|---|
| Too many images | Storage growth | Retention |
| Mutable tags | Wrong version deployed | Immutable tags/digests |
| Missing AcrPull | Pods cannot start | RBAC validation |
| Expired credentials | CI failure | OIDC/managed identity |
| Vulnerable image | Security risk | Image scanning |
| Wrong image tag | Deployment failure | Git SHA tagging |
| ACR outage/network issue | New pods cannot pull | Resilience/replication |
| Private DNS issue | Image pull failure | DNS monitoring |
| Registry permission drift | Deployment failure | IaC/RBAC audit |
| Large images | Slow deployment | Multi-stage builds |

---

# 34. Senior Production Approach

A senior DevOps engineer should not only ask:

> "Is the image available?"

The complete chain should be validated:

```text
Source Code
    ↓
Git Commit SHA
    ↓
CI Pipeline
    ↓
Unit Test
    ↓
SAST / Dependency Scan
    ↓
Docker Build
    ↓
Container Scan
    ↓
ACR Push
    ↓
Image Digest
    ↓
Helm Values
    ↓
AKS Deployment
    ↓
Kubelet Identity
    ↓
AcrPull
    ↓
ACR Network
    ↓
Pod Startup
    ↓
Readiness Probe
    ↓
Application Traffic
```

This gives complete deployment traceability.

---

# 35. Important ACR Commands

```bash
# Login
az acr login --name nexcartacr

# Get registry information
az acr show --name nexcartacr -o table

# Get login server
az acr show --name nexcartacr --query loginServer -o tsv

# List repositories
az acr repository list --name nexcartacr -o table

# List tags
az acr repository show-tags \
  --name nexcartacr \
  --repository product-service \
  -o table

# Show image
az acr repository show \
  --name nexcartacr \
  --repository product-service \
  --image 1.0.0

# Delete image
az acr repository delete \
  --name nexcartacr \
  --image product-service:1.0.0

# Check ACR public access
az acr show \
  --name nexcartacr \
  --query publicNetworkAccess

# Check AKS kubelet identity
az aks show \
  -g nexcart-rg \
  -n nexcart-aks \
  --query identityProfile.kubeletidentity
```

---

# 36. Interview Questions

### Q1. Why do we use ACR with AKS?

**Answer:**

> ACR provides private, Azure-native storage for container images. AKS can authenticate to ACR using managed identity and Azure RBAC, typically through the kubelet identity with the `AcrPull` role. This avoids distributing registry credentials to workloads.

---

### Q2. How does AKS pull an image from ACR?

```text
Pod scheduled
   ↓
AKS node/kubelet
   ↓
Managed Identity
   ↓
AcrPull RBAC
   ↓
ACR
   ↓
Container image
```

---

### Q3. What is the difference between `AcrPull` and Workload Identity?

> `AcrPull` allows the AKS node/kubelet identity to pull images from ACR. Workload Identity is generally used to allow a specific Kubernetes workload to access Azure resources such as Key Vault or Storage without storing long-lived credentials.

---

### Q4. Why should we avoid `latest`?

> `latest` is mutable. Two deployments using the same tag can potentially run different image content. Git SHA tags and image digests provide traceability and reproducibility.

---

### Q5. How would you troubleshoot `ImagePullBackOff`?

> I would first describe the pod and identify the exact image-pull error. Then I would validate the repository and tag in ACR, verify the AKS kubelet identity has `AcrPull`, and finally check network, DNS, private endpoint, firewall, and ACR availability if authentication is already correct.

---

### Q6. How do you implement secure GitLab → ACR authentication?

> I prefer GitLab OIDC/federated identity with Azure rather than long-lived client secrets. The pipeline authenticates to Azure using a federated identity, obtains access to ACR, builds the image, scans it, and pushes an immutable Git SHA or digest.

---

### Q7. What happens if ACR is accessible but the tag doesn't exist?

> AKS reports an image-pull failure such as `manifest unknown`. I would compare the image reference deployed by Helm with the actual repository tags in ACR and verify that the CI pipeline pushed the expected Git SHA.

---

### Q8. How would you reduce ACR storage?

> I would implement retention and cleanup policies, remove obsolete tags carefully, optimize Docker images using multi-stage builds, and monitor image growth. I would always retain the versions required for production rollback and compliance.

---

### Q9. How would you design ACR for production?

```text
Private/controlled access
        +
Azure RBAC
        +
Managed Identity
        +
Immutable image references
        +
Image scanning
        +
Retention policy
        +
Monitoring/auditing
        +
Geo-replication when required
```

---

### Q10. What is your NexCart image promotion strategy?

> GitLab builds each microservice once, tags the image using the Git commit SHA, scans it, and pushes it to ACR. The same immutable image is promoted across environments using Helm values rather than rebuilding the application for each environment. Production can reference the image digest for maximum reproducibility.

---

# 37. NexCart ACR Final Flow

```text
                    GitLab
                      |
                      v
                Git Commit SHA
                      |
                      v
                GitLab Pipeline
                      |
          +-----------+-----------+
          |                       |
       Testing                Security Scan
          |                       |
          +-----------+-----------+
                      |
                      v
                 Docker Build
                      |
                      v
             Container Image
                      |
                      v
        nexcartacr.azurecr.io
                      |
              +-------+-------+
              |       |       |
              v       v       v
          Product   Order   Payment
          Service  Service  Service
              |
              v
             AKS
              |
        Kubelet Identity
              |
           AcrPull
              |
              v
             ACR
              |
              v
          Image Pull
              |
              v
             Pod
              |
              v
        Readiness Probe
              |
              v
        Production Traffic
```

## 38. Senior-Level Key Takeaways

```text
ACR = Private container registry
AKS → ACR = Managed Identity + AcrPull
GitLab → Azure = Prefer OIDC/Federated Identity
Image Tag = Git SHA
Production = Prefer immutable digest
Never put secrets in Dockerfile/Git/Helm values
Use image scanning before deployment
Use retention policies for cleanup
Use private networking when required
Troubleshoot ImagePullBackOff from error → RBAC → image → network
Build once → promote the same artifact across environments
```