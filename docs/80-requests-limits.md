## Resource Requests and Limits

Kubernetes uses requests and limits to manage CPU and memory resources allocated to containers.

### 1. Requests

- Minimum resources requested by a container.
- Kubernetes uses requests when scheduling Pods onto nodes.
- Helps the scheduler determine whether a node has enough available resources.

### 2. Limits

- Maximum resources a container can consume.
- CPU: Container is throttled if it exceeds its CPU limit.
- Memory: Container can be terminated (OOMKilled) if it exceeds its memory limit.

### 3. Example 

```
containers:
  - name: order-service
    image: order-service:1.0
    resources:
      requests:
        cpu: "500m"
        memory: "512Mi"
      limits:
        cpu: "1"
        memory: "1Gi"
```


### 4. Important points

- `1000m` CPU = 1 CPU core.
- `500m` CPU = 0.5 CPU core.
- CPU is a compressible resource: exceeding the limit causes throttling. Slow down execution.
- Memory is non-compressible: exceeding the limit can cause OOM termination. Container may be killed.
- Requests influence Pod scheduling; limits constrain container resource consumption.
- Request = How much resource the container asks Kubernetes to reserve for scheduling.
- Limit = Maximum resource consumption allowed for the container.
