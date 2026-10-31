# kind


K8s in Docker.

https://kind.sigs.k8s.io/docs/user/quick-start/#installation

### Create a cluster
```
 kind create cluster --image kindest/node:v1.34.11@sha256:44e222ee2132dab25ff87301682f89eb82c7880ea3a1bf543bfe9708fd08d67d --name dev --config .\01-kind-cluster-config.yaml

```
