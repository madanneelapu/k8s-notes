### Kubernetes: Taints, Tolerations & Node Selectors

#### Taints

- Applied to Nodes.
- Repel Pods that don't tolerate the taint.
- Used to reserve nodes for specific workloads.

```
kubectl taint nodes node-2 workload=database:NoSchedule
```

#### Three taint effects 
- `NoSchedule` =	Don't schedule new non-tolerating Pods
- `PreferNoSchedule` =	Avoid scheduling, if possible
- `NoExecute` =	Evict existing non-tolerating Pods and prevent new ones

##### What if a taint on a node is altered after pods are already scheduled on it?

| Taint effect       | New Pods                                 | Existing Pods              |
| ------------------ | ---------------------------------------- | -------------------------- |
| `NoSchedule`       | Prevents scheduling (without toleration) | Remain running             |
| `PreferNoSchedule` | Avoids scheduling if possible            | Remain running             |
| `NoExecute`        | Prevents scheduling (without toleration) | Evicts non-tolerating Pods |


#### Tolerations

- Applied to Pods.
- Allow Pods to tolerate matching Node taints.
- Do not guarantee placement on that Node.

```yaml
tolerations:
  - key: workload
    operator: Equal
    value: database
    effect: NoSchedule
```

#### Node Selector

- Applied to Pods.
- Restricts Pods to Nodes with matching labels.
- Guarantees selection only from matching Nodes (if eligible).

```yaml
nodeSelector:
  workload: database
```

#### How they work together

| Mechanism     | Purpose                                  |
| ------------- | ---------------------------------------- |
| Taint         | Repel unwanted Pods                      |
| Toleration    | Allow Pod to tolerate taint              |
| Node Selector | Restrict Pod placement to matching Nodes |

Example:

Node configuration:

```
kubectl label nodes node-2 workload=database
kubectl taint nodes node-2 workload=database:NoSchedule
```

Pod configuration:

```yaml
spec:
  nodeSelector:
    workload: database
  tolerations:
    - key: workload
      operator: Equal
      value: database
      effect: NoSchedule
```

Result: Database Pod can be scheduled on Node 2. Ordinary Pods are repelled.

#### Scheduling process

1. Filtering: Eliminate ineligible nodes.
   - Node Selector
   - Required Node Affinity
   - Taints (unless tolerated)
   - Resource availability
2. Scoring: Rank remaining eligible nodes.
3. Binding: Assign Pod to selected Node.


- Taint + Toleration = Permission
- Node Selector = Placement restriction

