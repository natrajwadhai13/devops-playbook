---
title: "08-helm"
parent: "3. NexCard Interview"
grand_parent: "NexCart"
grand_grand_parent: "• Devops Project"
grand_grand_grand_parent: "1. DevOps"
nav_order: 8
---

# Helm

## 1. What is Helm?

**Helm** is the package manager for Kubernetes.

Instead of maintaining large numbers of raw Kubernetes YAML files, Helm packages Kubernetes resources into a reusable **Chart**.

For NexCart:

```text
GitLab
   ↓
Helm Chart
   ↓
Values
   ↓
Kubernetes Manifests
   ↓
AKS
```

Helm manages:

- Deployments
- Services
- Ingress
- ConfigMaps
- ServiceAccounts
- HPA
- SecretProviderClass
- Jobs
- Other Kubernetes resources

---

# 2. Why Helm is Required

Without Helm:

```text
deployment-dev.yaml
deployment-test.yaml
deployment-prod.yaml

service-dev.yaml
service-test.yaml
service-prod.yaml

ingress-dev.yaml
ingress-test.yaml
ingress-prod.yaml
```

This creates duplication and configuration drift.

With Helm:

```text
One reusable Chart
       +
Environment-specific values
       ↓
Dev / Test / Prod
```

Example:

```text
values-dev.yaml
values-test.yaml
values-prod.yaml
```

---

# 3. NexCart Helm Architecture

```text
                    Helm Chart
                        |
             +----------+----------+
             |                     |
        Templates               Values
             |                     |
             +----------+----------+
                        |
                        v
               Rendered YAML
                        |
                        v
                       AKS
```

Example:

```text
helm/nexcart/
├── Chart.yaml
├── values.yaml
├── values-dev.yaml
├── values-test.yaml
├── values-prod.yaml
└── templates/
    ├── deployment.yaml
    ├── service.yaml
    ├── ingress.yaml
    ├── serviceaccount.yaml
    ├── configmap.yaml
    ├── hpa.yaml
    └── secretproviderclass.yaml
```

---

# 4. Install and Verify Helm

Verify:

```bash
helm version
```

Example:

```text
version.BuildInfo{
  Version:"v3.x.x"
}
```

Helm 3 does not require the old Tiller server.

---

# 5. Helm Chart

Create a chart:

```bash
helm create nexcart
```

This creates:

```text
nexcart/
├── Chart.yaml
├── values.yaml
├── charts/
├── templates/
└── .helmignore
```

For production, remove unnecessary sample templates and maintain only the resources actually required.

---

# 6. Chart.yaml

Example:

```yaml
apiVersion: v2
name: nexcart
description: Helm chart for NexCart microservices
type: application
version: 1.0.0
appVersion: "1.0.0"
```

Important:

| Field | Purpose |
|---|---|
| `apiVersion` | Helm chart API version |
| `name` | Chart name |
| `description` | Chart description |
| `type` | Application/library |
| `version` | Helm chart version |
| `appVersion` | Application version |

---

# 7. Chart Version vs Application Version

These are different.

```text
Chart version:
1.2.0

Application version:
3.5.1
```

Chart version changes when Helm packaging/templates/configuration change.

Application version changes when the application itself changes.

Example:

```text
product-service image:
nexcartacr.azurecr.io/product-service:a81f92c
```

The image SHA is the strongest reference for the actual application artifact.

---

# 8. values.yaml

`values.yaml` contains default configuration.

Example:

```yaml
productService:
  replicaCount: 2

  image:
    repository: nexcartacr.azurecr.io/product-service
    tag: "a81f92c"

  service:
    port: 8082

  resources:
    requests:
      cpu: 250m
      memory: 256Mi
    limits:
      cpu: 1000m
      memory: 512Mi
```

Template consumes these values.

---

# 9. Environment-Specific Values

Example:

```text
values.yaml
values-dev.yaml
values-test.yaml
values-prod.yaml
```

Dev:

```yaml
productService:
  replicaCount: 1
```

Production:

```yaml
productService:
  replicaCount: 3
```

Deploy Dev:

```bash
helm upgrade --install nexcart ./helm/nexcart \
  -n nexcart-dev \
  --create-namespace \
  -f ./helm/nexcart/values-dev.yaml
```

Deploy Production:

```bash
helm upgrade --install nexcart ./helm/nexcart \
  -n nexcart-prod \
  --create-namespace \
  -f ./helm/nexcart/values-prod.yaml
```

---

# 10. Helm Templates

Example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: product-service
spec:
  replicas: {{ .Values.productService.replicaCount }}
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
          image: "{{ .Values.productService.image.repository }}:{{ .Values.productService.image.tag }}"
          ports:
            - containerPort: 8082
```

Helm replaces:

```text
.Values.productService.replicaCount
```

with the value from `values.yaml`.

---

# 11. Helm Template Rendering

Before deploying, render the Kubernetes YAML locally:

```bash
helm template nexcart ./helm/nexcart
```

Using production values:

```bash
helm template nexcart ./helm/nexcart \
  -f ./helm/nexcart/values-prod.yaml
```

This is one of the most useful Helm troubleshooting commands.

It allows you to inspect what Kubernetes will actually receive.

---

# 12. Helm Lint

Run:

```bash
helm lint ./helm/nexcart
```

Example:

```text
1 chart(s) linted, 0 chart(s) failed
```

Run this in CI before deployment.

---

# 13. Helm Dry Run

```bash
helm upgrade --install nexcart ./helm/nexcart \
  -n nexcart \
  --create-namespace \
  -f values-prod.yaml \
  --dry-run
```

This helps detect:

- Template errors
- Missing values
- Invalid manifests
- Incorrect configuration

For production pipelines, combine rendering with Kubernetes schema validation where available.

---

# 14. Helm Upgrade

Initial deployment:

```bash
helm upgrade --install nexcart ./helm/nexcart \
  -n nexcart \
  --create-namespace \
  -f values-prod.yaml
```

Later deployment:

```bash
helm upgrade nexcart ./helm/nexcart \
  -n nexcart \
  -f values-prod.yaml
```

`upgrade --install` is useful in CI/CD because it creates the release if it does not already exist.

---

# 15. Helm Release

A **release** is an installed instance of a Helm chart.

Example:

```text
Chart:
nexcarts

Release:
nexcart-prod
```

The same chart can create different releases:

```text
nexcart-dev
nexcart-test
nexcart-prod
```

Check:

```bash
helm list -A
```

Namespace-specific:

```bash
helm list -n nexcart-prod
```

---

# 16. Helm Status

```bash
helm status nexcart \
  -n nexcart
```

Useful information includes:

- Release status
- Revision
- Chart version
- Resources
- Notes

---

# 17. Helm History

```bash
helm history nexcart \
  -n nexcart
```

Example:

```text
REVISION   STATUS
1          superseded
2          superseded
3          deployed
```

This is extremely useful during production incidents.

---

# 18. Helm Rollback

If revision 3 is broken:

```bash
helm rollback nexcart 2 \
  -n nexcart
```

Verify:

```bash
helm status nexcart -n nexcart
```

Then:

```bash
kubectl rollout status \
  deployment/product-service \
  -n nexcart
```

Rollback is not a substitute for root-cause analysis.

---

# 19. Helm Rollback vs Kubernetes Rollback

### Helm rollback

```bash
helm rollback nexcart 2 -n nexcart
```

Rolls back the Helm release configuration.

### Kubernetes rollback

```bash
kubectl rollout undo deployment/product-service \
  -n nexcart
```

Rolls back a specific Deployment revision.

For a Helm-managed production application, use Helm rollback when the desired state itself needs to be reverted.

---

# 20. Helm Repository

Helm can package and distribute charts through a chart repository or OCI registry.

Traditional:

```text
Helm Repository
      ↓
Helm Chart
```

Modern enterprise pattern:

```text
OCI Registry
      ↓
Helm Chart
```

Azure Container Registry supports OCI artifacts, so Helm charts can also be stored alongside container artifacts where appropriate.

---

# 21. Package Helm Chart

```bash
helm package ./helm/nexcart
```

Output:

```text
nexcarts-1.0.0.tgz
```

The package can be published to a supported chart repository or OCI registry.

---

# 22. Helm OCI

Login to ACR:

```bash
az acr login --name nexcartacr
```

Package:

```bash
helm package ./helm/nexcart
```

Push:

```bash
helm push nexcart-1.0.0.tgz \
  oci://nexcartacr.azurecr.io/helm
```

Pull:

```bash
helm pull \
  oci://nexcartacr.azurecr.io/helm/nexcarts \
  --version 1.0.0
```

OCI-based Helm distribution is useful when the organization wants container images and charts managed through the same registry platform.

---

# 23. Helm Dependency

If a chart depends on other charts:

```yaml
dependencies:
  - name: redis
    version: 20.0.0
    repository: "oci://registry.example.com/charts"
```

Update dependencies:

```bash
helm dependency update ./helm/nexcarts
```

List:

```bash
helm dependency list ./helm/nexcarts
```

For NexCart, dependencies should be explicitly controlled and version-pinned rather than allowing unexpected upgrades.

---

# 24. Helm Helpers

Reusable template logic can be stored in:

```text
templates/_helpers.tpl
```

Example:

```yaml
{{- define "nexcarts.fullname" -}}
{{ .Release.Name }}-{{ .Chart.Name }}
{{- end }}
```

Use:

```yaml
metadata:
  name: {{ include "nexcarts.fullname" . }}
```

Helpers reduce duplicated template logic.

---

# 25. Named Templates

Example:

```yaml
{{- define "nexcarts.labels" }}
app.kubernetes.io/name: {{ .Chart.Name }}
app.kubernetes.io/instance: {{ .Release.Name }}
{{- end }}
```

Use:

```yaml
metadata:
  labels:
    {{- include "nexcarts.labels" . | nindent 4 }}
```

This provides consistent Kubernetes labels.

---

# 26. Helm Conditionals

Example:

```yaml
{{- if .Values.ingress.enabled }}
apiVersion: networking.k8s.io/v1
kind: Ingress
...
{{- end }}
```

Values:

```yaml
ingress:
  enabled: true
```

For Dev:

```yaml
ingress:
  enabled: false
```

This allows the same chart to support different environments.

---

# 27. Helm Loops

Example:

```yaml
{{- range .Values.env }}
- name: {{ .name }}
  value: {{ .value | quote }}
{{- end }}
```

Values:

```yaml
env:
  - name: NODE_ENV
    value: production
  - name: LOG_LEVEL
    value: info
```

This avoids duplicating YAML.

---

# 28. Helm Functions

Common functions:

```text
quote
default
required
toYaml
nindent
indent
include
tpl
lookup
```

Example:

```yaml
replicas: {{ default 2 .Values.productService.replicaCount }}
```

Required value:

```yaml
{{ required "image.repository is required" .Values.productService.image.repository }}
```

This is useful for preventing unsafe deployments caused by missing configuration.

---

# 29. `toYaml` and `nindent`

Useful for nested configuration.

Example:

```yaml
resources:
{{- toYaml .Values.productService.resources | nindent 12 }}
```

Values:

```yaml
resources:
  requests:
    cpu: 250m
    memory: 256Mi
  limits:
    cpu: 1000m
    memory: 512Mi
```

---

# 30. Helm Secrets

Do not put real passwords directly into:

```text
values.yaml
```

Bad:

```yaml
database:
  password: MyPassword123
```

Also avoid committing encrypted-looking values unless the encryption/decryption mechanism is properly managed.

Preferred approaches:

```text
Azure Key Vault
        ↓
Workload Identity
        ↓
Secrets Store CSI Driver
        ↓
Pod
```

or an approved external secret-management solution.

GitLab protected/masked variables can also be used for CI credentials where appropriate.

---

# 31. Helm + Key Vault

Typical architecture:

```text
Helm
 |
ServiceAccount
 |
Workload Identity
 |
Azure Managed Identity
 |
Key Vault
 |
Secrets Store CSI Driver
 |
Pod
```

Helm can deploy the Kubernetes integration resources without storing the actual secret value in Git.

Example chart:

```text
templates/
├── serviceaccount.yaml
├── secretproviderclass.yaml
└── deployment.yaml
```

---

# 32. Helm ServiceAccount

Example:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: product-service-sa
  namespace: {{ .Release.Namespace }}
  annotations:
    azure.workload.identity/client-id: "{{ .Values.identity.clientId }}"
```

Deployment:

```yaml
spec:
  template:
    metadata:
      labels:
        azure.workload.identity/use: "true"
    spec:
      serviceAccountName: product-service-sa
```

The exact annotations and configuration must match the AKS Workload Identity setup.

---

# 33. Helm HPA

Values:

```yaml
productService:
  autoscaling:
    enabled: true
    minReplicas: 2
    maxReplicas: 10
    targetCPUUtilizationPercentage: 70
```

Template:

```yaml
{{- if .Values.productService.autoscaling.enabled }}
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
...
{{- end }}
```

This allows different scaling policies per environment.

---

# 34. Helm Ingress

Values:

```yaml
ingress:
  enabled: true
  host: api.nexcart.example.com
```

Template:

```yaml
{{- if .Values.ingress.enabled }}
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nexcart-ingress
spec:
  rules:
    - host: {{ .Values.ingress.host }}
      http:
        paths:
          - path: /api/products
            pathType: Prefix
            backend:
              service:
                name: product-service
                port:
                  number: 8082
{{- end }}
```

---

# 35. Helm Image Management

Recommended values:

```yaml
productService:
  image:
    repository: nexcartacr.azurecr.io/product-service
    tag: "a81f92c"
```

Better production model:

```yaml
productService:
  image:
    repository: nexcartacr.azurecr.io/product-service
    digest: "sha256:..."
```

Template:

```yaml
image: "{{ .Values.productService.image.repository }}@{{ .Values.productService.image.digest }}"
```

This makes the deployed artifact immutable.

---

# 36. Build Once, Deploy Many with Helm

Pipeline:

```text
Git Commit
    ↓
Docker Build
    ↓
Security Scan
    ↓
Push Image to ACR
    ↓
Image SHA/Digest
    ↓
Helm Values
    ↓
Dev
    ↓
Test
    ↓
Production
```

Do not rebuild the image between environments.

Only environment-specific configuration should change.

---

# 37. GitLab CI/CD + Helm

Recommended stages:

```yaml
stages:
  - validate
  - test
  - security
  - build
  - deploy-dev
  - smoke
  - deploy-prod
```

Helm validation:

```bash
helm lint ./helm/nexcarts
```

Render:

```bash
helm template nexcarts \
  ./helm/nexcarts \
  -f ./helm/nexcarts/values-prod.yaml
```

Deploy:

```bash
helm upgrade --install nexcarts \
  ./helm/nexcarts \
  -n nexcart-prod \
  --create-namespace \
  -f ./helm/nexcarts/values-prod.yaml
```

---

# 38. Helm Deployment Gates

A mature pipeline should not immediately deploy production after a successful Docker build.

Recommended:

```text
Git Push
   ↓
Unit Tests
   ↓
SAST
   ↓
Dependency Scan
   ↓
Docker Build
   ↓
Image Scan
   ↓
Push ACR
   ↓
helm lint
   ↓
helm template
   ↓
Manifest Validation
   ↓
Deploy Dev
   ↓
Smoke Test
   ↓
Approval
   ↓
Deploy Production
```

---

# 39. Helm Atomic Deployment

For safer deployments:

```bash
helm upgrade --install nexcarts \
  ./helm/nexcarts \
  -n nexcart-prod \
  -f values-prod.yaml \
  --atomic \
  --timeout 10m
```

`--atomic` can roll back the release if the upgrade fails.

Use carefully and understand application behavior before relying on it as the only rollback mechanism.

---

# 40. Helm Wait

```bash
helm upgrade --install nexcarts \
  ./helm/nexcarts \
  -n nexcart-prod \
  -f values-prod.yaml \
  --wait \
  --timeout 10m
```

Helm waits for supported Kubernetes resources to become ready.

For production:

```text
--wait
+
appropriate timeout
+
readiness probes
+
post-deployment smoke tests
```

provide a stronger deployment validation strategy.

---

# 41. Helm Diff

A diff is useful before changing production.

Example:

```bash
helm diff upgrade nexcarts \
  ./helm/nexcarts \
  -n nexcart-prod \
  -f values-prod.yaml
```

This requires the Helm Diff plugin.

Review:

```text
Image
Replicas
Resources
Environment variables
Ingress
Service
Security settings
RBAC
```

before approving a production deployment.

---

# 42. Helm Troubleshooting

## Template error

Run:

```bash
helm lint ./helm/nexcarts
```

Then:

```bash
helm template nexcarts ./helm/nexcarts \
  -f values-prod.yaml
```

Look for:

```text
YAML syntax
Missing value
Incorrect indentation
Invalid template expression
Wrong variable path
```

---

# 43. Helm Release Failed

Check:

```bash
helm status nexcarts -n nexcart
```

History:

```bash
helm history nexcarts -n nexcart
```

Kubernetes:

```bash
kubectl get pods -n nexcart
kubectl get events -n nexcart --sort-by=.lastTimestamp
```

Then:

```bash
kubectl describe pod <pod> -n nexcart
kubectl logs <pod> -n nexcart
```

Helm status tells you about the release.

Kubernetes tells you what the workload is actually doing.

---

# 44. Helm Upgrade Succeeded but Application Is Down

This is an important production scenario.

Helm can successfully apply Kubernetes resources while the application remains unhealthy.

Check:

```bash
helm status nexcart -n nexcart
```

Then:

```bash
kubectl get deploy,pods,svc,ingress -n nexcart
```

Then:

```bash
kubectl get endpoints -n nexcart
```

Then logs:

```bash
kubectl logs <pod> -n nexcart
```

Possible root causes:

```text
Bad application configuration
Wrong image
Wrong port
Readiness failure
Dependency failure
Database connection issue
Ingress routing issue
Secret issue
```

**Helm success does not equal application success.**

---

# 45. Helm Drift

Helm stores the desired release state, but resources can also be modified manually.

Example:

```bash
kubectl edit deployment product-service -n nexcart
```

Now Kubernetes differs from the intended Helm configuration.

This creates operational drift.

Best practice:

```text
Git
 ↓
Helm
 ↓
AKS
```

Make Git/Helm the source of truth.

Avoid manual production changes unless required for emergency mitigation, and reconcile the change back into Git afterward.

---

# 46. Helm and Terraform

Terraform and Helm solve different problems.

### Terraform

Typically manages infrastructure:

```text
Resource Group
AKS
ACR
VNet
Subnet
Key Vault
Managed Identity
Role Assignments
```

### Helm

Typically manages Kubernetes applications:

```text
Deployment
Service
Ingress
HPA
ConfigMap
ServiceAccount
SecretProviderClass
```

Architecture:

```text
Terraform
    ↓
Azure Infrastructure
    ↓
AKS
    ↓
Helm
    ↓
NexCart Workloads
```

Do not unnecessarily make Terraform and Helm manage the same Kubernetes resources.

---

# 47. Helm and Argo CD

Argo CD is a GitOps deployment tool.

Possible architecture:

```text
Git
 ↓
Helm Chart
 ↓
Argo CD
 ↓
AKS
```

In a GitLab CI-driven architecture:

```text
GitLab CI
 ↓
Helm
 ↓
AKS
```

If Argo CD is introduced later, establish one clear deployment ownership model. Avoid having both GitLab CI and Argo CD independently modifying the same release without a deliberate GitOps design.

---

# 48. Helm Production Best Practices

```text
✓ Version charts
✓ Pin dependencies
✓ Use environment-specific values
✓ Keep secrets out of Git
✓ Use immutable image references
✓ Run helm lint
✓ Run helm template
✓ Validate manifests
✓ Use readiness/liveness probes
✓ Use resource requests/limits
✓ Use RBAC
✓ Use Workload Identity
✓ Use controlled production approvals
✓ Keep rollback history
✓ Monitor after deployment
✓ Store chart source in Git
✓ Avoid manual kubectl changes
✓ Keep chart templates reusable
```

---

# 49. Common Helm Mistakes

### Mistake 1: Hardcoding image

Bad:

```yaml
image: nexcartacr.azurecr.io/product-service:latest
```

Better:

```yaml
image:
  repository: nexcartacr.azurecr.io/product-service
  tag: "a81f92c"
```

---

### Mistake 2: Secrets in values

Bad:

```yaml
dbPassword: "MyPassword123"
```

Better:

```text
Azure Key Vault
       ↓
Workload Identity
       ↓
Pod
```

---

### Mistake 3: No environment separation

Bad:

```text
One values.yaml for everything
```

Better:

```text
values.yaml
values-dev.yaml
values-test.yaml
values-prod.yaml
```

---

### Mistake 4: No validation

Bad:

```text
helm upgrade
```

Better:

```text
helm lint
 ↓
helm template
 ↓
manifest validation
 ↓
deploy
```

---

### Mistake 5: Manual production modification

Bad:

```bash
kubectl edit deployment
```

and never update Git.

Better:

```text
Change Git
 ↓
Review
 ↓
Pipeline
 ↓
Helm
 ↓
AKS
```

---

# 50. Production Day-2 Helm Problems

| Problem | Impact | Solution |
|---|---|---|
| Wrong values | Bad configuration | Validate values |
| Wrong image tag | Failed/incorrect deployment | Git SHA/digest |
| Template bug | Deployment failure | Lint/template |
| Secret missing | Pod failure | Key Vault integration |
| Chart drift | Unpredictable state | Git as source of truth |
| Failed upgrade | Application outage | Rollback |
| Dependency update | Unexpected behavior | Pin versions |
| Manual kubectl change | Drift | Reconcile to Git |
| Wrong namespace | Deployment confusion | Explicit namespace |
| Timeout | Release failure | Fix readiness/dependency issue |
| Bad probe | Traffic outage | Tune probes |
| Resource mismatch | Pending/OOM | Tune requests/limits |

---

# 51. Important Helm Commands

```bash
# Version
helm version

# Create chart
helm create nexcart

# Lint
helm lint ./helm/nexcarts

# Render templates
helm template nexcarts ./helm/nexcarts

# Render production
helm template nexcarts ./helm/nexcarts \
  -f ./helm/nexcarts/values-prod.yaml

# List releases
helm list -A

# Status
helm status nexcarts -n nexcart

# History
helm history nexcarts -n nexcart

# Install/upgrade
helm upgrade --install nexcarts \
  ./helm/nexcarts \
  -n nexcart \
  --create-namespace \
  -f values-prod.yaml

# Dry run
helm upgrade --install nexcarts \
  ./helm/nexcarts \
  -n nexcart \
  -f values-prod.yaml \
  --dry-run

# Rollback
helm rollback nexcarts <revision> -n nexcart

# Package
helm package ./helm/nexcarts

# Dependency update
helm dependency update ./helm/nexcarts

# Dependency list
helm dependency list ./helm/nexcarts
```

---

# 52. Senior Interview Questions

## Q1. Why do you use Helm?

> Helm gives us a reusable and version-controlled packaging mechanism for Kubernetes resources. Instead of maintaining separate YAML files for every environment, we maintain reusable templates and environment-specific values. This reduces duplication and configuration drift.

---

## Q2. Explain your NexCart Helm structure.

> We maintain a common Helm chart containing Deployments, Services, Ingress, ServiceAccounts, ConfigMaps, HPA and Azure integration resources where required. Common defaults are maintained in `values.yaml`, while environment-specific settings are maintained in separate values files. GitLab validates the chart and deploys it to AKS.

---

## Q3. What is the difference between `values.yaml` and templates?

> `values.yaml` contains configuration data. Templates contain the Kubernetes resource definitions and reference those values using Helm's templating language.

---

## Q4. How do you troubleshoot a Helm deployment failure?

> I first run `helm lint` and `helm template` to identify chart-level problems. If the chart renders correctly, I inspect `helm status`, release history, Kubernetes events, Pods, Services and application logs. I separate Helm/template issues from actual runtime issues.

---

## Q5. Helm deployment succeeded but Pods are failing. What do you do?

> I treat Helm success and application health as separate signals. I check Pod status, events, logs, image, environment variables, probes, Service endpoints and dependencies. The release may be successfully installed while the application is still unhealthy.

---

## Q6. How do you rollback?

```bash
helm history nexcart -n nexcart
helm rollback nexcart <revision> -n nexcart
helm status nexcart -n nexcart
```

> After rollback, I verify Pod readiness, application health and user traffic rather than considering the rollback complete merely because Helm reports success.

---

## Q7. How do you manage secrets with Helm?

> I don't store production secrets directly in Git or normal Helm values. For Azure workloads, I prefer Workload Identity with Key Vault and an appropriate secret integration mechanism. Helm deploys the integration configuration, while the secret remains in the external secret store.

---

## Q8. How do you prevent configuration drift?

> Git is the source of truth. Helm templates and values are version controlled, production changes go through review and CI/CD, and manual changes are avoided. If emergency `kubectl` changes are necessary, I reconcile them back into Git afterward.

---

## Q9. Helm vs Terraform?

> Terraform manages infrastructure such as AKS, ACR, VNets, Key Vault and managed identities. Helm manages application resources inside Kubernetes. I keep the ownership boundary clear to avoid two tools managing the same resource.

---

## Q10. Helm vs Argo CD?

> Helm is a Kubernetes package manager and templating/deployment mechanism. Argo CD is a GitOps continuous delivery controller that continuously reconciles Kubernetes state from Git. Helm can be used by Argo CD to render applications, but they solve different layers of the deployment problem.

---

## Q11. How would you implement a production-safe Helm deployment?

```text
Git
 ↓
Code Review
 ↓
helm lint
 ↓
helm template
 ↓
Security/Manifest Validation
 ↓
Docker Image Scan
 ↓
ACR Push
 ↓
Deploy Dev
 ↓
Smoke Test
 ↓
Approval
 ↓
Production Helm Upgrade
 ↓
Wait/Readiness Validation
 ↓
Smoke Test
 ↓
Monitoring
```

---

## Q12. What is your rollback strategy?

> I maintain Helm release history and immutable application images. If a release causes production impact, I first stop further rollout, assess the blast radius, rollback to the last known-good release if appropriate, validate application health, and then investigate the root cause. The rollback gives us service recovery; the RCA prevents recurrence.

---

# 53. Senior Helm Deployment Checklist

```text
Chart
✓ Chart.yaml
✓ Versioned chart
✓ Clean templates
✓ Reusable helpers
✓ No unnecessary generated templates

Configuration
✓ values.yaml
✓ values-dev.yaml
✓ values-test.yaml
✓ values-prod.yaml
✓ No plaintext production secrets

Images
✓ ACR
✓ Git SHA
✓ Immutable digest where required
✓ Image scan

Kubernetes
✓ Deployment
✓ Service
✓ Ingress
✓ ServiceAccount
✓ HPA
✓ ConfigMap
✓ Probes
✓ Resource requests/limits

Security
✓ RBAC
✓ Workload Identity
✓ Key Vault
✓ Least privilege

CI/CD
✓ helm lint
✓ helm template
✓ Manifest validation
✓ Dev deployment
✓ Smoke test
✓ Production approval
✓ Rollback capability

Operations
✓ Helm history
✓ Monitoring
✓ Logs
✓ Events
✓ Drift control
✓ Chart dependency management
```

# 54. Final NexCart Helm Flow

```text
                    GitLab Repository
                           |
                           v
                    Helm Chart + Values
                           |
              +------------+------------+
              |            |            |
           Dev Values   Test Values   Prod Values
              |            |            |
              +------------+------------+
                           |
                           v
                      helm lint
                           |
                           v
                    helm template
                           |
                           v
                    Validation/Scan
                           |
                           v
                    GitLab Pipeline
                           |
                           v
                          AKS
                           |
              +------------+-------------+
              |            |             |
              v            v             v
          Deployment     Service       Ingress
              |
              v
             Pods
              |
       +------+------+
       |             |
 ServiceAccount   ConfigMap
       |
 Workload Identity
       |
   Azure Key Vault
```

## Core Senior Principle

> **Helm should be treated as the application deployment contract for Kubernetes: Git defines the desired configuration, Helm renders it consistently, CI/CD validates and promotes it, and AKS executes it. The objective is not simply to make `helm upgrade` succeed, but to make deployments reproducible, auditable, secure, rollback-safe, and operationally predictable.**