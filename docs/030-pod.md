# Pod

#### Sample pod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    env: demo
    type: frontend
spec:
  containers:
  - name: nginx-container
    image: nginx
    ports:
    - containerPort: 80
```

#### Creating a pod

kubectl create -f .\10-nginx-pod.yaml

#### List pods
kubectl get pods -o wide

#### Describe Pod 
kubectl describe pod nginx-pod

#### pod labels
```
PS C:\madan_work\k8s-kind-code> kubectl get pods nginx-pod --show-labels
NAME        READY   STATUS    RESTARTS   AGE   LABELS
nginx-pod   1/1     Running   0          31m   env=demo,type=frontend
```

#### Create pod.yaml with dry run

```shell
PS C:\madan_work\k8s-kind-code> kubectl run nginx --image=nginx --dry-run=client -o yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```
