# Horizontal Pod Autoscaling (HPA)

## 1. What is HPA?

- HPA stands for Horizontal Pod Autoscaler.
- Automatically increases or decreases the number of Pod replicas based on resource utilization or other metrics.
- This is called scale out / scale in.
- Commonly uses CPU and memory utilization.
- Works with Deployments and ReplicaSets.
- It is a Kubernetes-native feature.
- Requires Metrics Server for CPU/memory-based autoscaling.

## 2. How HPA works

1. Metrics Server collects CPU and memory usage from Kubelets.
2. HPA reads the metrics.
3. HPA compares actual utilization against the configured target.
4. HPA adjusts the desired replica count of the Deployment.
5. Deployment creates or removes Pods.

Request flow: `Kubelet → Metrics Server → HPA → Deployment → Pods`

## 3. Prerequisite: Metrics Server setup

Metrics Server is not installed by default in your kind cluster.

Step 1 – Install Metrics Server

```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

Step 2 – Configure it for kind

For local kind clusters, kubelet certificate verification can cause problems.

Edit the Metrics Server Deployment:

```
kubectl edit deployment metrics-server -n kube-system
```

Under the Metrics Server container's `args`, add:

```
- --kubelet-insecure-tls
```

This disables kubelet TLS certificate verification. Use it only as a local development workaround, not in production.

Step 3 – Verify installation

```
kubectl rollout status deployment/metrics-server -n kube-system
kubectl top nodes
kubectl top pods -A
```


## 4. Create a Spring Boot Deployment

Create `order-service.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order-service
spec:
  selector:
    matchLabels:
      app: order-service          # Identifies which Pods the Deployment manages
  template:
    metadata:
      labels:
        app: order-service        # Assigns labels to Pods created by the Deployment
    spec:
      containers:
        - name: order-service
          image: order-service:1.0
          ports:
            - containerPort: 8080
          resources:
            requests:
              cpu: 200m
              memory: 256Mi
            limits:
              cpu: "1"
              memory: 512Mi
```

Apply:

```
kubectl apply -f order-service.yaml
```

Important: Resource requests are necessary for CPU utilization-based HPA calculations.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: order-service
spec:
  selector:                # matches the same Pod labels.
    app: order-service         
  ports:
    - port: 8080
      targetPort: 8080
```


## 5. Create HPA

Create `order-service-hpa.yaml`:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: order-service-hpa
spec:
  scaleTargetRef:           # explicitly identifies the Deployment that HPA should scale.
    apiVersion: apps/v1
    kind: Deployment
    name: order-service

  minReplicas: 2
  maxReplicas: 8

  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70
```

Apply:

```
kubectl apply -f order-service-hpa.yaml
```

Check:

```
kubectl get hpa
```

Example output:

```
NAME               REFERENCE                 TARGETS   MINPODS   MAXPODS   REPLICAS
order-service-hpa  Deployment/order-service  25%/70%   2         8         2
```

## 6. Understand the configuration

| Property             | Meaning                        |
| -------------------- | ------------------------------ |
| `scaleTargetRef`     | Deployment that HPA manages    |
| `minReplicas`        | Minimum number of Pods         |
| `maxReplicas`        | Maximum number of Pods         |
| `metrics`            | Metric used for scaling        |
| `averageUtilization` | Target average CPU utilization |

In our example:

- CPU request per Pod = 200m.
- Target CPU utilization = 70%.
- Target CPU usage = 140m per Pod on average.

If average CPU usage increases significantly above 140m, HPA may add more Pods.

If usage decreases, HPA can remove Pods, but never below 2.

## 7. Observe autoscaling

Useful commands:

```
# View HPA
kubectl get hpa

# Continuously watch HPA
kubectl get hpa -w

# Watch Pods
kubectl get pods -w

# Detailed HPA information
kubectl describe hpa order-service-hpa

# View current resource consumption
kubectl top pods
```



