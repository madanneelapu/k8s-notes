## Multi-container Pods

A Pod can contain multiple containers that work together.

### 1. Init Container

- Runs before the main application container.
- Used for initialization or prerequisite tasks.
- Must complete successfully before the application starts.
- Runs sequentially if multiple init containers exist.
- Usually exits after completing its task.

Spring Boot example: Wait for PostgreSQL to become available.

```
spec:
  initContainers:
    - name: wait-for-db
      image: postgres:16
      command:
        - sh
        - -c
        - |
          until pg_isready -h postgres-service; do
            sleep 2
          done

  containers:
    - name: spring-boot
      image: order-service:1.0
      ports:
        - containerPort: 8080
```

Execution: Init Container → Spring Boot Container

### 2. Sidecar Container

- Runs alongside the main application container.
- Provides supporting functionality to the application.
- Shares the Pod's network namespace.
- Can share volumes with the main container.
- Usually runs for the lifetime of the application.

Spring Boot example: Fluent Bit collects application logs.

```
spec:
  containers:
    - name: spring-boot
      image: order-service:1.0
      volumeMounts:
        - name: app-logs
          mountPath: /app/logs

    - name: fluent-bit
      image: fluent/fluent-bit:3.2
      volumeMounts:
        - name: app-logs
          mountPath: /app/logs
          readOnly: true

  volumes:
    - name: app-logs
      emptyDir: {}
```

### Native Sidecar Containers

- A Kubernetes feature that allows sidecars to be defined as restartable Init Containers.
- Declared under `initContainers` with `restartPolicy: Always`.
- Starts before the main application container.
- Continues running alongside the application.
- During normal Pod shutdown, application containers terminate before sidecars.
- Provides better startup and shutdown ordering.

Example: Spring Boot + Fluent Bit

```
spec:
  initContainers:
    - name: fluent-bit
      image: fluent/fluent-bit:3.2
      restartPolicy: Always

  containers:
    - name: spring-boot
      image: order-service:1.0
```

Key distinction: Traditional sidecar → `containers`; Native sidecar → `initContainers` with `restartPolicy: Always`.

### 3. Comparison

| Init Container              | Sidecar Container          |
| --------------------------- | -------------------------- |
| Runs before application     | Runs alongside application |
| Initialization tasks        | Supporting tasks           |
| Usually exits               | Usually keeps running      |
| Example: DB readiness check | Example: Log forwarding    |

Remember:

- Init Container = Prepare before starting.
- Sidecar Container = Support while running.
