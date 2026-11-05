### Kubernetes DaemonSet

- Ensures one copy of a Pod runs on every eligible node.
- Automatically creates a Pod when a new eligible node joins.
- Automatically removes the Pod when a node is no longer eligible.

#### Eligible Nodes

A node is eligible if it satisfies the DaemonSet's scheduling requirements.

Factors include: `nodeSelector`, Node affinity, Taints and tolerations, Resource availability

#### Common Use Cases

| Purpose        | Examples             |
| -------------- | -------------------- |
| Log collection | Fluent Bit           |
| Monitoring     | Node Exporter        |
| Networking     | CNI plugins          |
| Security       | Node security agents |
| Storage        | Storage agents       |

#### DaemonSet vs Deployment

| Feature       | DaemonSet              | Deployment                |
| ------------- | ---------------------- | ------------------------- |
| Pod placement | Every eligible node    | Based on replicas         |
| New node      | Automatically adds Pod | No automatic per-node Pod |
| Scaling       | Follows eligible nodes | Replica count             |
| Usage         | Node-level agents      | Applications              |

#### YAML Example

```YAML
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluent-bit
spec:
  selector:
    matchLabels:
      app: fluent-bit
  template:
    metadata:
      labels:
        app: fluent-bit
    spec:
      containers:
        - name: fluent-bit
          image: fluent/fluent-bit:latest
```

#### Useful Commands

```
kubectl get daemonsets -A
kubectl describe daemonset fluent-bit
kubectl get pods -o wide
kubectl delete daemonset fluent-bit
```

- Deployment manages application replicas; DaemonSet manages node-level Pods across eligible nodes.
