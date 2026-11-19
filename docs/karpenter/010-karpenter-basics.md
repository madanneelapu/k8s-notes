- k8s node auto-scalers (Cluster Autoscaler/Karpenter) checks periodicaly for pending unschedulable pods.
- If needed, adds new nodes so that Scheduler can schedule the pods.
- usecase-1: in case of microservices; HPA increases replicas based on metrics.
- usecase-2: in case of batch jobs; all replica pods will kick in immediately.

- 
