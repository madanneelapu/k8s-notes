# Health Probes
Kubernetes health probes help determine whether a container is healthy and ready to serve traffic.

There are three types:
1. **Liveness** Probe: Is the application alive?
    - Checks whether the application is still functioning.
    - If it fails, Kubernetes restarts the container.
  
2. **Readiness** Probe: Is the application ready to handle requests?
    - If it fails, Kubernetes removes the Pod from Service endpoints.
    - Container is NOT restarted. Only Stop sending traffic to Pod.
  
3. **Startup** Probe: Has the application finished starting?
    - Useful for applications with slow startup.
    - Other probes are disabled until startup succeeds.
    - If it fails beyond the configured threshold, Kubernetes restarts the container.


### Kubernetes Deployment YAML

This includes all probe configuration options.

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: order-service

spec:
  replicas: 2

  selector:
    matchLabels:
      app: order-service

  template:
    metadata:
      labels:
        app: order-service

    spec:
      containers:
        - name: order-service
          image: order-service:1.0

          ports:
            - containerPort: 8080

          # 1. STARTUP PROBE
          startupProbe:
            httpGet:
              path: /actuator/health
              port: 8080
              scheme: HTTP       # HTTP or HTTPS

            initialDelaySeconds: 0  # Wait before first probe
            periodSeconds: 10       # Probe every 10 seconds
            timeoutSeconds: 2       # Probe timeout
            failureThreshold: 30   # Allow up to 30 consecutive failures
            successThreshold: 1    # One success marks startup complete

          # 2. LIVENESS PROBE
          livenessProbe:
            httpGet:
              path: /actuator/health/liveness
              port: 8080
              scheme: HTTP

            initialDelaySeconds: 0
            periodSeconds: 10
            timeoutSeconds: 2
            failureThreshold: 3    # Restart after 3 consecutive failures
            successThreshold: 1

          # 3. READINESS PROBE
          readinessProbe:
            httpGet:
              path: /actuator/health/readiness
              port: 8080
              scheme: HTTP

            initialDelaySeconds: 0
            periodSeconds: 5
            timeoutSeconds: 2
            failureThreshold: 3    # Mark Pod not ready after 3 failures
            successThreshold: 1
```

### Other available probe configurations

You can use these instead of `httpGet`.

TCP Socket

```yaml
readinessProbe:
  tcpSocket:
    port: 8080
```

Checks whether a TCP connection can be established.

Exec

```yaml
livenessProbe:
  exec:
    command:
      - cat
      - /tmp/healthy
```

Executes a command inside the container. Exit code `0` means success.

gRPC

```yaml
livenessProbe:
  grpc:
    port: 9090
    service: liveness
```

Uses the gRPC health checking protocol.

### Important additional configuration

Kubernetes also supports:

| Configuration                   | Purpose                                                                      |
| ------------------------------- | ---------------------------------------------------------------------------- |
| `terminationGracePeriodSeconds` | Grace period for container termination after a failed liveness/startup probe |
| `host`                          | HTTP probe target hostname                                                   |
| `httpHeaders`                   | Custom HTTP headers                                                          |
| `port`                          | Container port or named port                                                 |
| `scheme`                        | HTTP or HTTPS                                                                |


### Example Spring Boot configuration – `application.yml`

```yaml
server:
  port: 8080

management:
  endpoints:
    web:
      exposure:
        include: health  # Expose the health endpoint over HTTP

  endpoint:
    health:
      probes:
        enabled: true    # Enable Kubernetes liveness/readiness health groups

      show-details: always

  health:
    livenessstate:
      enabled: true      # Enable liveness health indicator
    readinessstate:
      enabled: true      # Enable readiness health indicator
```

