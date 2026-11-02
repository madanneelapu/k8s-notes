## Authentication

- via kube config file.
- stored in `<user_home>/.kube/config`.
- contains clusters, users and contexts.
- Don't need to modify the file manually.


## Authorization
- RBAC is widely used.

### Role and RoleBinding
- Role: Defines what actions are permitted on which resources within a **namespace**.
- RoleBinding: Assigns that Role's permissions to a user, group, or service account.

#### Example:
- A Role allows `get`, `list` and `watch` on Pods in a given **namespace**.
- A RoleBinding assigns that Role to user `john`.
- If you do not specify a namespace when creating a Role or RoleBinding in Kubernetes, they default to the `default` namespace.

Result: John can view Pods in that namespace but cannot create or delete them.

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

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: john-pod-reader
  namespace: dev
subjects:
  - kind: User
    name: john
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

### ClusterRole and ClusterRoleBinding
- ClusterRole: Defines permissions at the cluster level. Can cover cluster-wide resources (Nodes, Namespaces) or namespaced resources (Pods, Deployments).
- ClusterRoleBinding: Assigns those permissions cluster-wide to a user, group, or service account.

#### Example A

ClusterRole – Allow viewing Nodes

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: node-reader
rules:
  - apiGroups: [""]
    resources: ["nodes"]
    verbs: ["get", "list", "watch"]
```

ClusterRoleBinding – Assign to John

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: john-node-reader
subjects:
  - kind: User
    name: john
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: node-reader
  apiGroup: rbac.authorization.k8s.io
```

#### Example B
Important: A ClusterRole can also be assigned through a RoleBinding, restricting its namespaced permissions to that particular namespace.

ClusterRole – Allow viewing Pods

```yaml
kind: ClusterRole
metadata:
  name: pod-reader
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list"]
```

RoleBinding – Assign to John with namespace set

```yaml
kind: RoleBinding
metadata:
  name: pod-reader-binding
  namespace: dev
subjects:
  - kind: User
    name: john
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

### Few important commands

```
kubectl get roles -A
kubectl get rolebindings -A

kubectl get clusterroles
kubectl get clusterrolebindings

kubectl describe role pod-reader -n dev
kubectl describe rolebinding john-pod-reader -n dev

kubectl describe clusterrole node-reader -o yaml
kubectl describe clusterrolebinding john-node-reader -o yaml

# Check permissions - Useful for debugging permission issues
kubectl auth can-i get pods --as=john -n dev

kubectl auth can-i delete pods --as=john -n dev

kubectl auth can-i list nodes --as=john

kubectl auth can-i --list --as=john -n dev

```
