- k8s node auto-scalers (Cluster Autoscaler/Karpenter) checks periodicaly for pending unschedulable pods.
- If needed, adds new nodes so that Scheduler can schedule the pods.
- usecase-1: in case of microservices; HPA increases replicas based on metrics.
- usecase-2: in case of batch jobs; all replica pods will kick in immediately.

### Cluster Autoscaler
- Node provision latency: CA-> ASG -> NodeGroup -> EC2
- karpenter directly calls ec2 api. node group needed. provisioning takes minutes
- CA needs nodegroup management.
- karpenter does not need nodegroup; and can choose from various ec2 types. can intelligently provision ec2 based on the pod requirements

### karpenter
- efficient node autoscaler
- AWS -> CNCF
- automatically launches appropriate worker nodes without node groups
