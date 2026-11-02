## Kubernetes ServiceAccounts


### What is a ServiceAccount?

- A ServiceAccount (SA) provides an identity to applications and workloads running inside Kubernetes Pods.
- It is used when a workload needs to authenticate with the Kubernetes API server.
- A default service account shall be created in each namespace.

- User account → Human identity.
- ServiceAccount → Application/workload identity.

### Why do we need ServiceAccounts?

Real-world use cases:

| Workload           | Why it needs a ServiceAccount         |
| ------------------ | ------------------------------------- |
| Jenkins            | Deploy applications to Kubernetes     |
| Prometheus         | Discover Pods and Services            |
| Argo CD            | Manage Kubernetes resources           |
| cert-manager       | Manage certificates and secrets       |
| Custom application | Read Kubernetes resources through API |

Not every application needs Kubernetes API access.

#### Creating a ServiceAccount

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: order-service
  namespace: dev
```

```
kubectl apply -f service-account.yaml
```

List ServiceAccounts:

```
kubectl get sa -A
kubectl get sa -n dev
```

Get details:

```
kubectl describe sa order-service -n dev
kubectl get sa order-service -n dev -o yaml
```

#### Assigning a ServiceAccount to a Pod

Use `serviceAccountName` in the Pod specification.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: order-pod
  namespace: dev
spec:
  serviceAccountName: order-service
  containers:
    - name: order
      image: order-service:1.0
```

Now the Pod runs with the `order-service` identity.

If you don't specify a ServiceAccount, Kubernetes uses the namespace's `default` ServiceAccount.

### Authentication using ServiceAccount

When a Pod uses a ServiceAccount:

1. Kubernetes provides a ServiceAccount token to the Pod.
2. The application uses the token to authenticate with the API server.
3. The API server validates the token and identifies the ServiceAccount.
4. The API server evaluates its permissions.

The token is typically mounted automatically at:

```
/var/run/secrets/kubernetes.io/serviceaccount/
```

Important files:

| File      | Purpose                   |
| --------- | ------------------------- |
| token     | Authentication token      |
| ca.crt    | API server CA certificate |
| namespace | Pod's namespace           |

### ServiceAccount and Secrets 

Earlier Kubernetes versions automatically created long-lived token Secrets for ServiceAccounts.

Since Kubernetes 1.24, this is no longer the default.

Modern Kubernetes uses projected, short-lived ServiceAccount tokens that are automatically mounted into Pods by default.

You can explicitly generate a token:

```
kubectl create token order-service -n dev
```

### ServiceAccount with RBAC

A ServiceAccount provides identity, but it doesn't automatically grant permissions to access Kubernetes resources.

You must assign permissions using RBAC.

Example: Allow `order-service` to read Pods in the `dev` namespace.

Role:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: dev
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list", "watch"]
```

RoleBinding:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: order-service-binding
  namespace: dev
subjects:
  - kind: ServiceAccount
    name: order-service
    namespace: dev
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

Now the `order-service` ServiceAccount can read Pods in `dev`.

#### Important commands

| Command                                                                           | Purpose                  |
| --------------------------------------------------------------------------------- | ------------------------ |
| `kubectl get sa -A`                                                               | List all ServiceAccounts |
| `kubectl describe sa order-service -n dev`                                        | View details             |
| `kubectl get sa order-service -n dev -o yaml`                                     | View YAML                |
| `kubectl create sa order-service -n dev`                                          | Create ServiceAccount    |
| `kubectl create token order-service -n dev`                                       | Generate token           |
| `kubectl auth can-i get pods --as=system:serviceaccount:dev:order-service -n dev` | Check permissions        |



