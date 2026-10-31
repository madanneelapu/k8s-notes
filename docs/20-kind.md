# kind


K8s in Docker.

https://kind.sigs.k8s.io/docs/user/quick-start/#installation


### Create a config file

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
    extraPortMappings:
    - containerPort: 30001
      hostPort: 30001
  - role: worker
  - role: worker
```


### Create a cluster
```
 kind create cluster --image kindest/node:v1.34.11@sha256:44e222ee2132dab25ff87301682f89eb82c7880ea3a1bf543bfe9708fd08d67d --name dev --config .\01-kind-cluster-config.yaml

```

```powershell
cgroup v1 is deprecated in Kubernetes and will not be supported in a future kind release, please upgrade to cgroup v2
Creating cluster "dev" ...
 • Ensuring node image (kindest/node:v1.34.11) 🖼️  ...
 ✓ Ensuring node image (kindest/node:v1.34.11) 🖼️
 • Preparing nodes 📦 📦 📦   ...
 ✓ Preparing nodes 📦 📦 📦 
 • Writing configuration 📜  ...
 ✓ Writing configuration 📜
 • Starting control-plane 🕹️  ...
 ✓ Starting control-plane 🕹️
 • Installing CNI 🔌  ...
 ✓ Installing CNI 🔌
 • Installing StorageClass 💾  ...
 ✓ Installing StorageClass 💾
 • Joining worker nodes 🚜  ...
 ✓ Joining worker nodes 🚜
Set kubectl context to "kind-dev"
You can now use your cluster with:

kubectl cluster-info --context kind-dev

Thanks for using kind! 😊

```
