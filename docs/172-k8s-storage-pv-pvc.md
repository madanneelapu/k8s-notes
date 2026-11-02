# Kubernetes Storage 

### PersistentVolume (PV)
- Represents **actual storage** available to the cluster.
- Cluster-level resource.
- Can represent AWS EBS, EFS, NFS, etc.
- Defines storage capacity, access modes and reclaim policy.

### PersistentVolumeClaim (PVC)
- A **request for storage** by an application.
- Specifies required storage size and access modes.
- Kubernetes binds a suitable PV to the PVC.
- A Pod uses PVC to access storage.


### Access Modes

| Mode | Description                    |
| ---- | ------------------------------ |
| RWO  | Read/write from a single node  |
| ROX  | Read-only from multiple nodes  |
| RWX  | Read/write from multiple nodes |
| RWOP | Read/write from a single Pod   |

Note: RWO allows multiple Pods on the same node to potentially use the volume.

### Reclaim Policies

Defines what happens to storage when its PVC is deleted.

| Policy | Behaviour                                         |
| ------ | ------------------------------------------------- |
| Retain | Keeps underlying storage; manual cleanup required |
| Delete | Deletes PV and underlying storage, if supported   |



```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: app-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /mnt/data/app
    type: DirectoryOrCreate
```
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: app-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 2Gi
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          volumeMounts:
            - name: app-storage
              mountPath: /usr/share/nginx/html
      volumes:
        - name: app-storage
          persistentVolumeClaim:
            claimName: app-pvc
```

### Pod Volume Mounting
- PVC is referenced inside the Pod specification.
- `volumes` references the PVC.
- `volumeMounts` specifies the container directory where storage is mounted.
- Data survives Pod deletion/recreation as long as the underlying storage is retained.


### Limitations
1. Data is tied to a particular node.
2. Node failure can make data unavailable.
3. Moving the Pod to another node doesn't move the data.
4. No automatic replication or backup.

> PV/PVC are Kubernetes abstractions. They don't require external storage, but for production use EBS/EFS CSI drivers.

## External Storage

### CSI Driver (Container Storage Interface)
- Standard interface for integrating external storage systems with Kubernetes.
- Allows storage vendors to provide storage integrations.
- AWS provides CSI drivers for EBS and EFS.

### AWS EKS Storage

| AWS Service | Use case                 | Access                                |
| ----------- | ------------------------ | ------------------------------------- |
| EBS         | Persistent block storage | Typically RWO                         |
| EFS         | Shared filesystem        | RWX                                   |
| S3          | Object storage           | Not natively mounted as a standard PV |


### StorageClass
- Defines how storage should be dynamically provisioned.
- Eliminates the need to manually create PVs.
- Specifies provisioner, storage parameters and reclaim policy.

Static provisioning:
1. Admin creates PV.
2. Application creates PVC.
3. Kubernetes binds PVC to PV.

Dynamic provisioning:
1. Admin configures StorageClass.
2. Application creates PVC.
3. Kubernetes dynamically creates PV.
4. PVC gets bound to PV.
