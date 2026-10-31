# K8s Architecture

- **Node** is a virtual machine
- **Control plane/master node** is a VM with Administrative components
- **Worker node** is where the actual containers run.
- Contianers are encapsulated inside **pod**. Pod can contain one or more containers. Containers share  the resources of a pod.


-  Client(kubectl) interacts with the cluster using **ApiServer** 
- **Scheduler** decides which pod to be scheduled on which node based on different factors.
- **Controller Manager** is responsible for managing various controllers.

- **ETCD Server** is Key-value database that stores the cluster state and configuration

- **Kubelet** is a Node-level agent that helps container management and receives instructions from Api server.

    The Kubelet does not rely on a direct push or request from the API Server. Instead, it establishes a watch stream to the API Server and continuously monitors for updates to resources it's responsible for. When a pod is scheduled to a node, the Kubelet on that node detects the change via its watch stream, retrieves the pod's spec, and proceeds to pull the necessary container images and run the pod. So, the API Server doesn't directly notify the Kubelet; it's the Kubelet that actively watches for updates.

- **Kube proxy** is responsible for  Pod-to-pod networking and communication

[Refer for diagram](https://github.com/piyushsachdeva/CKA-2024/tree/main/Resources/Day05#kubernetes-architecture)


# Kubernetes Deployment Sequence Diagram

```mermaid
sequenceDiagram
    actor Dev as Developer
    participant K as kubectl
    participant API as API Server
    participant E as etcd
    participant CM as Controller Manager
    participant S as Scheduler
    participant KL as Kubelet
    participant CR as Container Runtime

    Dev->>K: kubectl apply -f deployment.yaml
    K->>API: Submit Deployment YAML
    API->>API: Authenticate, authorize and validate
    API->>E: Save desired state
    E-->>API: Acknowledge
    API-->>K: Deployment accepted

    CM->>API: Watch Deployment changes
    API-->>CM: Deployment detected
    CM->>API: Create ReplicaSet
    API->>E: Persist ReplicaSet
    CM->>API: Create Pods
    API->>E: Persist Pod definitions

    S->>API: Watch unscheduled Pods
    API-->>S: New unscheduled Pod
    S->>S: Select suitable worker node
    S->>API: Assign Pod to worker node
    API->>E: Save node assignment

    KL->>API: Watch assigned Pods
    API-->>KL: Pod assigned to this node
    KL->>CR: Pull image and start container
    CR-->>KL: Container started
    KL->>API: Update Pod status
    API->>E: Persist Pod status

    K->>API: Request deployment status
    API-->>K: Deployment status
    K-->>Dev: Display deployment status
```
