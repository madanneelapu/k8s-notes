## Kubernetes ConfigMaps and Secrets

Both are used to inject configuration into containers without hardcoding it into application images.

### 1. ConfigMap

Stores non-sensitive configuration.

Example: `application.properties` values, feature flags, service URLs.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: order-service-config
data:
  SPRING_PROFILES_ACTIVE: prod
  PAYMENT_SERVICE_URL: http://payment-service:8080
  LOG_LEVEL: INFO
```

### 2. Secret

Stores sensitive configuration.

Example: passwords, API keys, database credentials.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: order-service-secret
type: Opaque
stringData:
  DB_USERNAME: order_user
  DB_PASSWORD: mypassword
```

`stringData` accepts plain text. Kubernetes converts it to Base64 when storing it.

Important: Base64 is encoding, not encryption. Use encryption at rest and restrict RBAC access.

### 3. Inject into Spring Boot Deployment

Both can be injected as environment variables.

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

          envFrom:
            - configMapRef:
                name: order-service-config
            - secretRef:
                name: order-service-secret
```

Spring Boot can access these directly:

```yaml
spring:
  datasource:
    url: ${DB_URL}
    username: ${DB_USERNAME}
    password: ${DB_PASSWORD}
```





  Service URLs         API keys
  Feature flags        Passwords
```
