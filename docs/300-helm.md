# HELM

- Package manager for kubernetes
- HELM bundles all k8s manifests together; its called `Helm Chart`
- Helm Charts are uploaded to a `repository` eg: artifacthub.io.
- To install a package, just pull the chart from repository and create a `release` out of it. Release is a running instance of the chart.
- Charts are reusable. Each deployment creates a new release.

### Installation and usage

- Refer https://helm.sh/docs/intro/install/
- Windows: `choco install kubernetes-helm`

#### commands
- `helm version`  displays version.
- `helm create my-chart`  creates a new directory with all necessary files.

#### Installing ArgoCD with HELM

```
kubectl cluster-info

kubectl get nodes -o wide

# Add the Argo Helm repository
helm repo add argo https://argoproj.github.io/argo-helm

# Update the repository
helm repo update

# lists available chart versions
helm search repo argo/argo-cd -l

kubectl create namespace argocd

# Install Argo CD
helm install argocd argo/argo-cd `
  --namespace argocd `
  --version 10.11.6

# Check Helm release
helm list -n argocd

kubectl get pods -n argocd

kubectl get svc -n argocd

# Access Argo CD UI
kubectl port-forward svc/argocd-server -n argocd 8080:443

# Get the initial admin password
$encoded = kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}"

[System.Text.Encoding]::UTF8.GetString(
    [System.Convert]::FromBase64String($encoded)
)


```
