
### GitOps

- GitOps is a practice, not a tool.
- Git is the source of truth for the desired state.
- A GitOps controller monitors Git and the Kubernetes cluster.
- It continuously compares the desired state (Git) with the actual state (cluster).
- It reconciles differences to bring the cluster back to the desired state.
- Eg: ArgoCD, FluxCD, JenkinsX, Spinnaker(not fully).

#### Advantages

1. Version control: Track every infrastructure/application change.
2. Auditability: Know who changed what and when.
3. Security: Prefer pull-based deployments; cluster credentials need not be exposed to CI pipelines.
4. Self-healing: Automatically correct configuration drift.
5. Continuous reconciliation: Continuously maintain the desired state.
6. Rollback: Revert to a previous Git commit.

### Architecture (ArgoCD)
- API Server:	UI, CLI, authentication and API requests.
- Repo Server:	Connects to Git and retrieves Kubernetes manifests.
- Application Controller:	Compares desired state with actual cluster state and performs reconciliation.
- Redis:	Caching and optimization.
- Dex:	Provides authentication through external identity providers (OIDC, LDAP connectors, GitHub, etc.).
  Dex is optional; direct OIDC integration is also supported.

