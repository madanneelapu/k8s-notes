## CoreDNS

In Kubernetes:

- CoreDNS runs in the `kube-system` namespace.
- Typically runs as a Deployment with 2 replicas (cluster-dependent).
- Exposes a Service named `kube-dns`.
- The Service provides a stable ClusterIP for DNS queries.

```
kubectl get pods -n kube-system -l k8s-app=kube-dns
kubectl get svc -n kube-system kube-dns
```

## What happens when a Pod starts?

Kubernetes configures the Pod's DNS settings.

Check:

```
kubectl exec -it my-pod -- cat /etc/resolv.conf
```

Example:

```
nameserver 10.100.0.10
search production.svc.cluster.local svc.cluster.local cluster.local
options ndots:5
```

- `nameserver`: IP of the `kube-dns` Service.
- `search`: DNS suffixes Kubernetes tries automatically.
- `ndots`: Influences when DNS search suffixes are applied.

The Pod does not need to know individual CoreDNS Pod IPs.

## How does CoreDNS know how to resolve names?

CoreDNS uses a ConfigMap mounted into its container.

Check:

```
kubectl get configmap coredns -n kube-system -o yaml
```


## How does DNS resolution work?

Example: Order Service wants to call Payment Service.

```
Order Pod
    |
    | DNS query: payment-service
    v
kube-dns Service (ClusterIP)
    |
    v
CoreDNS Pod
    |
    | Looks up Kubernetes Service records
    v
Payment Service ClusterIP
    |
    v
Payment Pod
```

CoreDNS watches Kubernetes Services and EndpointSlices and maintains the DNS records.

