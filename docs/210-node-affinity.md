## Kubernetes – Node Affinity

### What is Node Affinity?

- Controls which nodes a Pod can be scheduled on based on node labels.
- Advanced alternative to `nodeSelector`.
- Supports mandatory and preferred scheduling rules.

### Types of Node Affinity

| Type                                              | Behaviour                                                                 |
| ------------------------------------------------- | ------------------------------------------------------------------------- |
| `requiredDuringSchedulingIgnoredDuringExecution`  | Hard requirement. Pod won't schedule if no matching node exists.          |
| `preferredDuringSchedulingIgnoredDuringExecution` | Soft requirement. Scheduler prefers matching nodes but can choose others. |

`IgnoredDuringExecution`: Changes to node labels after scheduling do not evict the running Pod.

### Node Affinity Operators

| Operator       | Meaning                                    |
| -------------- | ------------------------------------------ |
| `In`           | Value matches one of the specified values  |
| `NotIn`        | Value does not match specified values      |
| `Exists`       | Label key must exist                       |
| `DoesNotExist` | Label key must not exist                   |
| `Gt`           | Label value greater than specified integer |
| `Lt`           | Label value less than specified integer    |

- `In`, `NotIn`, `Gt`, `Lt` require `values`.
- `Exists`, `DoesNotExist` must not have `values`.

### Examples

Required Node Affinity

```
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
        - matchExpressions:
            - key: disktype
              operator: In
              values: ["ssd"]
```

Preferred Node Affinity

```
affinity:
  nodeAffinity:
    preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 80
        preference:
          matchExpressions:
            - key: disktype
              operator: In
              values: ["ssd"]
```

- `weight`: Preference strength (1–100).
- If no matching node is available, the Pod can be scheduled on another suitable node.

### Production Use Cases

1. Memory-intensive workloads: Schedule Redis or Elasticsearch on high-memory nodes.
2. GPU workloads: Schedule AI inference workloads on GPU nodes.
3. Environment separation: Schedule production workloads on production-designated nodes.
4. SSD-backed workloads: Prefer SSD nodes for database workloads.
5. Workload isolation: Run critical services on designated nodes.

### NodeSelector vs Node Affinity

| Feature              | nodeSelector | nodeAffinity |
| -------------------- | ------------ | ------------ |
| Exact label matching | Yes          | Yes          |
| Expression operators | No           | Yes          |
| Mandatory scheduling | Yes          | Yes          |
| Preferred scheduling | No           | Yes          |
| Complexity           | Simple       | Flexible     |

Production preference: Use `nodeSelector` for simple requirements and `nodeAffinity` when more scheduling flexibility is needed.

### Combining Taints, Tolerations and Node Affinity

Production use case: Dedicated GPU nodes for AI workloads.

Step 1: Label GPU node

```
kubectl label node gpu-node-1 workload=ai
```

Step 2: Taint GPU node

```
kubectl taint nodes gpu-node-1 workload=ai:NoSchedule
```

Step 3: AI Pod configuration

```
spec:
  tolerations:
    - key: workload
      operator: Equal
      value: ai
      effect: NoSchedule

  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: workload
                operator: In
                values: ["ai"]
```

### How they work together

| Mechanism     | Responsibility                                          |
| ------------- | ------------------------------------------------------- |
| Taint         | Prevents general workloads from scheduling on GPU nodes |
| Toleration    | Allows AI workloads onto GPU nodes                      |
| Node affinity | Ensures AI workloads select GPU nodes                   |

Expected behaviour:

| Pod                              | GPU node | General node |
| -------------------------------- | -------- | ------------ |
| AI Pod (toleration + affinity)   | Allowed  | Not selected |
| General Pod (without toleration) | Blocked  | Allowed      |

Key takeaway:

- Taints repel Pods.
- Tolerations allow Pods onto tainted nodes.
- Node affinity attracts/selects Pods for matching nodes.
