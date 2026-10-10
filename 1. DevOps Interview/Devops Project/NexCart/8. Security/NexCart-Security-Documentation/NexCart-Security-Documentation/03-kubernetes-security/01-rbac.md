---
title: "• kubernetes-security"
parent: "• Security_Documentation"
grand_parent: "8. Security"
grand_grand_parent: "NexCart"
grand_grand_grand_parent: "• Devops Project"
grand_grand_grand_grand_parent: "1. DevOps"
nav_order: 4
---


- 01-rbac
- 02-serviceaccount
- 03-securitycontext
- 04-networkpolicy


# Kubernetes RBAC

## THEORY

RBAC controls which identities can perform actions against the Kubernetes API.

Objects:
- Role
- RoleBinding
- ClusterRole
- ClusterRoleBinding

Role is namespace-scoped; ClusterRole can provide cluster-level or reusable permissions.

## NEXCART

Example:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: nexcart-reader
  namespace: nexcart
rules:
  - apiGroups: [""]
    resources: ["pods", "services"]
    verbs: ["get", "list", "watch"]
```

## VERIFICATION
```bash
kubectl auth can-i get pods -n nexcart --as=system:serviceaccount:nexcart:nexcart-sa
```

## TROUBLESHOOTING
`forbidden` means the identity lacks required authorization. Check Roles and bindings and grant least privilege.

## INTERVIEW
"RBAC provides Kubernetes authorization. I prefer namespace-scoped permissions and least privilege rather than cluster-admin access."


========================================

# Kubernetes ServiceAccount

## THEORY
A ServiceAccount gives a workload an identity inside Kubernetes.

## NEXCART
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: nexcart-sa
  namespace: nexcart
```

Deployment:
```yaml
spec:
  template:
    spec:
      serviceAccountName: nexcart-sa
```

Use it with RBAC and minimum required permissions.

For Azure AKS, Workload Identity is the preferred pattern for cloud-resource access in the future phase.

## VERIFICATION
```bash
kubectl get serviceaccount -n nexcart
```

## INTERVIEW
"A ServiceAccount gives pods a Kubernetes identity. I combine it with RBAC and least privilege."

========================================
# Kubernetes SecurityContext

## THEORY
SecurityContext controls security properties for pods and containers.

Common controls:
- runAsNonRoot
- runAsUser/runAsGroup
- allowPrivilegeEscalation
- Linux capabilities
- readOnlyRootFilesystem
- seccomp

## EXAMPLE
```yaml
securityContext:
  runAsNonRoot: true
  allowPrivilegeEscalation: false
```

## VERIFICATION
```bash
kubectl get pod <pod-name> -n nexcart -o yaml
kubectl exec -n nexcart <pod-name> -- id
```

## TROUBLESHOOTING
If a workload fails after enabling non-root, check whether the image/application expects root or needs writable directories. Fix permissions instead of removing the control blindly.

## INTERVIEW
"SecurityContext reduces container privileges and supports non-root execution and reduced privilege escalation."


========================================

# Kubernetes NetworkPolicy

## THEORY
NetworkPolicy controls pod ingress/egress when supported by the cluster networking implementation.

Concepts:
- ingress
- egress
- podSelector
- namespaceSelector
- default deny
- allow-list

## NEXCART MODEL
```text
Default Deny
  -> allow frontend -> API
  -> allow API -> required services
  -> allow services -> required databases
```

Example:
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: product-service-policy
  namespace: nexcart
spec:
  podSelector:
    matchLabels:
      app: product-service
  policyTypes:
    - Ingress
```

Add actual ingress rules based on the application traffic flow.

## VERIFICATION
```bash
kubectl get networkpolicy -n nexcart
kubectl describe networkpolicy <name> -n nexcart
```

## INTERVIEW
"NetworkPolicy provides pod-level network segmentation. I prefer default deny plus explicit allow rules."


========================================