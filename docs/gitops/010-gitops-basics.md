
### GitOps

- GitOps is a practice, not a tool.
- Git is the source of truth for the desired state.
- A GitOps controller monitors Git and the Kubernetes cluster.
- It continuously compares the desired state (Git) with the actual state (cluster).
- It reconciles differences to bring the cluster back to the desired state.

#### Advantages

1. Version control: Track every infrastructure/application change.
2. Auditability: Know who changed what and when.
3. Security: Prefer pull-based deployments; cluster credentials need not be exposed to CI pipelines.
4. Self-healing: Automatically correct configuration drift.
5. Continuous reconciliation: Continuously maintain the desired state.
6. Rollback: Revert to a previous Git commit.

