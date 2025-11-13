---
title: "• Kuberntes command"
parent: "• Kubernetes"
grand_parent: "• 3.2 Orchestration"
great_grand_parent: 3. DevOps Core Tools
nav_order: 3
has_children: true
---

<h2 align="center">🧭 Kubernetes Command Reference Cheat Sheet</h2>

<table>
<tr>
<td width="50%" valign="top">

<h3>🔹 DaemonSets (ds)</h3>

| Command | Description |
|----------|--------------|
| `kubectl apply -f manifest_file.yaml` | Apply configuration file |
| `kubectl get daemonset` | List all daemonsets |
| `kubectl describe ds <daemonset_name> -n <namespace>` | Show details of a DaemonSet |
| `kubectl edit daemonset <daemonset_name>` | Edit a DaemonSet |
| `kubectl delete daemonset <daemonset_name>` | Delete a DaemonSet |
| `kubectl rollout status daemonset/<name>` | View rollout status |

---

<h3>🔹 Deployments (deploy)</h3>

| Command | Description |
|----------|--------------|
| `kubectl get deploy` | List deployments |
| `kubectl create deploy <name> --image=<image>` | Create a new deployment |
| `kubectl rollout status deploy/<name>` | Check rollout |
| `kubectl rollout undo deploy/<name>` | Rollback deployment |
| `kubectl scale deploy <name> --replicas=<n>` | Scale deployment |
| `kubectl delete deploy <name>` | Delete deployment |

---

<h3>🔹 Pods (po)</h3>

| Command | Description |
|----------|--------------|
| `kubectl get pods` | List pods |
| `kubectl exec -it <pod> -- /bin/sh` | Access shell |
| `kubectl describe pod <pod>` | Show pod details |
| `kubectl logs <pod>` | Show logs |
| `kubectl logs -f <pod>` | Follow logs |
| `kubectl top pod` | Show resource usage |
| `kubectl delete pod <pod>` | Delete pod |
| `kubectl label pod <pod> key=value` | Add label |
| `kubectl annotate pod <pod> key=value` | Add annotation |

---

<h3>🔹 Nodes (no)</h3>

| Command | Description |
|----------|--------------|
| `kubectl get nodes` | List cluster nodes |
| `kubectl describe nodes` | Show node details |
| `kubectl top node` | Show node resource usage |
| `kubectl cordon <node>` | Mark unschedulable |
| `kubectl uncordon <node>` | Mark schedulable |
| `kubectl drain <node>` | Drain node for maintenance |
| `kubectl taint node <node> key=value:NoSchedule` | Add taint |
| `kubectl label node <node> key=value` | Add label |

---

<h3>🔹 Namespaces (ns)</h3>

| Command | Description |
|----------|--------------|
| `kubectl get ns` | List namespaces |
| `kubectl create ns <name>` | Create namespace |
| `kubectl delete ns <name>` | Delete namespace |
| `kubectl top ns <namespace>` | Show resource usage |

</td>
<td width="50%" valign="top">

<h3>🔹 Services (svc)</h3>

| Command | Description |
|----------|--------------|
| `kubectl get svc` | List services |
| `kubectl expose deploy <name> --port=<port>` | Expose a deployment |
| `kubectl delete svc <name>` | Delete service |
| `kubectl describe svc <name>` | Show details |

---

<h3>🔹 ReplicaSets (rs)</h3>

| Command | Description |
|----------|--------------|
| `kubectl get rs` | List ReplicaSets |
| `kubectl describe rs <name>` | Show ReplicaSet details |
| `kubectl scale rs <name> --replicas=<n>` | Scale ReplicaSet |

---

<h3>🔹 StatefulSets (sts)</h3>

| Command | Description |
|----------|--------------|
| `kubectl get sts` | List StatefulSets |
| `kubectl delete sts <name> --cascade=false` | Delete StatefulSet only |

---

<h3>🔹 Events (ev)</h3>

| Command | Description |
|----------|--------------|
| `kubectl get events` | List recent events |
| `kubectl get events --field-selector type=Warning` | Show warnings only |
| `kubectl get events --field-selector involvedObject.kind!=Pod` | Exclude Pod events |

---

<h3>🔹 Logs & Monitoring</h3>

| Command | Description |
|----------|--------------|
| `kubectl logs <pod>` | View logs |
| `kubectl logs -f <pod>` | Stream logs |
| `kubectl logs --since=1h <pod>` | Logs for last 1 hour |
| `kubetail <pod_prefix>` | Logs from multiple pods (Kubetail) |
| `kubectl top pod` | Pod resource usage |
| `kubectl top node` | Node resource usage |

---

<h3>🔹 YAML / Manifest</h3>

| Command | Description |
|----------|--------------|
| `kubectl apply -f manifest.yaml` | Apply configuration |
| `kubectl create -f manifest.yaml` | Create resources |
| `kubectl create -f ./dir/` | Create from directory |
| `kubectl create -f https://url/file.yaml` | Create from URL |
| `kubectl delete -f manifest.yaml` | Delete resources |

---

<h3>🔹 Cluster & Config</h3>

| Command | Description |
|----------|--------------|
| `kubectl cluster-info` | Display master & service info |
| `kubectl version` | Show Kubernetes version |
| `kubectl config view` | Show kubeconfig details |
| `kubectl api-resources` | List API resources |
| `kubectl get all --all-namespaces` | List all resources |

---

<h3>🔹 Optional Flags</h3>

| Flag | Example | Description |
|------|----------|-------------|
| `-n` | `kubectl get pods -n kube-system` | Namespace selection |
| `-o wide` | `kubectl get pods -o wide` | Detailed output |
| `-l` | `kubectl get pods -l app=nginx` | Label selector |
| `--sort-by` | `kubectl get pods --sort-by=.metadata.name` | Sort by field |
| `--field-selector` | `kubectl get events --field-selector type!=Normal` | Filter by field |

</td>
</tr>
</table>
