
#### NodePort
- Application is exposed on a port i.e.; node port
- Default NodePort range is 30000–32767.
- You can specify a static nodePort, or let Kubernetes allocate one automatically.
- targetPort: Port where the application container listens; is the destination port on the selected Pod.
- port: Port exposed by the Kubernetes Service inside the cluster. It is the Service's port and can be accessed by other Pods and clients inside the cluster.
- nodePort: Port exposed on each node for external access.

```mermaid
flowchart LR
    A["External Client"] -->|"NodePort: 30080"| B["Service: 8080"]
    B -->|"targetPort: 80"| C["Nginx Pod: 80"]
```


