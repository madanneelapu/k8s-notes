### Replication Controller

- Older mechanism for maintaining Pod replicas.

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx-rc
  labels:
    env: demo
spec:
  template:
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

  replicas: 3
```

### ReplicaSet

- Modern mechanism for maintaining Pod replicas. Preffered.
- Can manage other pods using selector.matchLabels

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
  labels:
    env: demo
spec:
  template:
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

  replicas: 3
  selector:
    matchLabels:
      env: demo
```

#### Explain
kubectl explain rs

#### Scale
kubectl scale --replicas=10 rs/nginx-rs

#### Delete
 kubectl delete rs/nginx-rs

### Deployment
- Does rolling update.
- No downtime; unlike RS and RC.
- Rollback possible

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-rs
  labels:
    env: demo
spec:
  template:
    metadata:
      name: nginx-deploy
      labels:
        env: demo
        type: frontend
    spec:
      containers:
        - name: nginx-container
          image: nginx
          ports:
            - containerPort: 80

  replicas: 3
  selector:
    matchLabels:
      env: demo

```


```powershell
PS C:\madan_work\k8s-kind-code> kubectl apply -f .\22-deployment.yaml  
deployment.apps/nginx-rs created
PS C:\madan_work\k8s-kind-code> kubectl get all
NAME                            READY   STATUS    RESTARTS   AGE
pod/nginx-rs-5497ddf4d8-75k4x   1/1     Running   0          9s
pod/nginx-rs-5497ddf4d8-7qjnw   1/1     Running   0          9s
pod/nginx-rs-5497ddf4d8-tch89   1/1     Running   0          9s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   68m

NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-rs   3/3     3            3           9s

NAME                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-rs-5497ddf4d8   3         3         3       9s
PS C:\madan_work\k8s-kind-code> 


```


