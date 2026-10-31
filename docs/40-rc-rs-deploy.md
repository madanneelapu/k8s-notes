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

 

