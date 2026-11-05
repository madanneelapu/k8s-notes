## Kubernetes – Static Pods & Manual Scheduling

#### Static Pods

- Managed directly by the kubelet, not the API server.
- Defined using YAML manifests on the node.
- Default manifest directory (kubeadm):

```
/etc/kubernetes/manifests/
```

- Kubelet continuously monitors the manifests and maintains the Pods.
- API server creates mirror Pods for visibility.
- Cannot be managed through Deployments or other controllers.

Use case: Running control-plane components such as API Server, Scheduler and Controller Manager.

#### Manual Scheduling

- Kubernetes Scheduler normally selects a node for a Pod.
- Manual scheduling allows us to bypass the scheduler.
- Two approaches:
  1. Specify `nodeName` directly.
  2. Manually bind a Pod to a node using the Binding API. (revisit this topic; only if really needed)

#### nodeName Example

Specify the target node directly in the Pod specification.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeName: worker-1
  containers:
    - name: nginx
      image: nginx
```

- Bypasses the Kubernetes Scheduler.
- Kubelet on the specified node starts the Pod.
- If the node is unavailable, the Pod cannot start.
- No automatic rescheduling if the node fails.

