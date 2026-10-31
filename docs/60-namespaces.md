# Namespaces
- default: Namespace used when you don't explicitly specify one.
- kube-system: Contains Kubernetes system components such as CoreDNS and other cluster services.
- kube-public: Publicly readable namespace, generally used for cluster information.
- kube-node-lease: Contains node heartbeat information.

### Pod IPs
- Pod IPs are generally unique across the cluster.
- Pod IPs are dynamically assigned and can change when Pods are recreated.
- Don't use Pod IPs for application-to-application communication. (though pods can communicate with IPs).
- Use Service DNS names instead. 

### Service communication across namespaces
Pods in different namespaces can communicate using the Service DNS name.
- Same namespace: `http://payment-service:8080`
- Different namespace: `http://payment-service.finance:8080`
- Fully qualified domain name (FQDN): `payment-service.finance.svc.cluster.local`

### Commands
#### DNS configuration
Check a Pod's DNS configuration:
```
kubectl exec -it <pod-name> -n <namespace> -- cat /etc/resolv.conf
```

#### Namespaces
```
 kubectl get ns
```

```
 kubectl get all -n kube-system
```
