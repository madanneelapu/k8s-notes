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

Example:
- A Role allows `get`, `list` and `watch` on Pods in a given **namespace**.
- A RoleBinding assigns that Role to user `john`.

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


