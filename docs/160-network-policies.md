## Kubernetes Network Policies

- A Kubernetes NetworkPolicy controls network communication between pods and external endpoints.
- Think of it as a **firewall for Kubernetes pods**.
- By default, Kubernetes allows unrestricted pod-to-pod communication.
- NetworkPolicies are **stateful**. If a connection is allowed, response traffic is automatically allowed.
- NetworkPolicy enforcement requires CNI support.
- Multiple policies are additive (allowed traffic is combined).
- NetworkPolicies are namespace-scoped.
- A pod becomes isolated for ingress or egress when selected by a policy of that type.
- Ingress and Egress isolation are independent.

### Example Architecture

- Frontend → Backend: Allowed
- Backend → Database: Allowed
- Other pods → Database: Blocked

### NetworkPolicy YAML Example

Allow only backend pods to access PostgreSQL on port 5432.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: database-policy
spec:
  # Pods protected by this policy
  podSelector:
    matchLabels:
      app: postgres

  # Control incoming traffic
  policyTypes:
    - Ingress

  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: backend
      ports:
        - protocol: TCP
          port: 5432
```

#### Important fields

| Field | Purpose |
|---|---|
| `podSelector` | Selects pods protected by the policy |
| `policyTypes` | Specifies Ingress and/or Egress |
| `ingress` | Defines allowed incoming traffic |
| `egress` | Defines allowed outgoing traffic |
| `from` | Defines allowed traffic sources |
| `to` | Defines allowed traffic destinations |
| `ports` | Defines allowed ports |


### NetworkPolicies in Amazon EKS

- EKS uses Amazon VPC CNI by default.
- Amazon VPC CNI supports Kubernetes NetworkPolicies, but its NetworkPolicy feature must be enabled separately.

| Component | Responsibility |
|---|---|
| Amazon VPC CNI | Provides pod networking and IP allocation |
| NetworkPolicy | Defines traffic rules |
| CNI enforcement | Enforces the traffic rules |

