## K8s Networking


<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/f47a4a09-6051-4595-8886-c3883f4bbf14" />


- Docker was used to be bundled into k8s core. This is heavy.
- But then OCI came: Open Container Initiative. Plug and play any container runtime. Specifications for container images and execution.
- OCI defines:
  - Image Specification: How container images are structured.
  - Runtime Specification: How containers should be executed.
  - Distribution Specification: How images are distributed between registries and clients.
- Docker re-architected to become OCI complaint.

### Docker working
- When we run `docker run ...` the request goes to docker daemon.
- Daemon does not start container.
- `containerd` manages container lifecycle, images, snapshots and execution.
- `containerd-shim` manages the container process independently of containerd.
- `runc` creates and starts the container using Linux kernel features. It exits after starting the container.

  ```
  Docker CLI -> Docker Daemon (dockerd) -> containerd -> containerd-shim -> runc -> Container
  ```

#### Changes in k8s
- Docker shim was a Kubernetes-specific integration component. It is different from containerd-shim.
- Kubernetes removed dockershim in v1.24, not Docker support itself.
- WebAssembly workloads are possible through compatible runtimes such as runwasi.


### k8s networking
- k8s worker node has kublet and CRI - Container runtime interface. CRI is also a specification.
- CRI helps kublet talk to Container runtime. Interface between kubelet and container runtime.
- ContainerD is a container runtime. CRI implementation.
- Kubernetes container startup architecture:
  ```
  kubectl -> API server -> scheduler -> API Server (Pod binding) -> Kubelet (selected node) -> CRI -> Container D -> containerd-shim -> runC.
  ```
- now things go to CNI to attach the network interface. CNI is a specification. Interface for container networking.
- no default networking implementation. instead we have to opt one CNI implementation: eg: Calico, Cilium, flanel, weave etc.
- There should be something to attach IP address to pods; enable them to communicate across cluster etc.
- When Kubernetes creates a Pod:
  1. Container runtime creates the Pod sandbox.
  2. A pause container typically holds the Pod's network namespace.
  3. CNI configures networking for that sandbox.
  4. Application containers join the same network namespace.
- Consequently:
  - All containers in a Pod share one IP address.
  - They share the same network interfaces.
  - They can communicate through localhost.
  - They share port space, so they cannot independently bind the same port.
- exec onto a pod and run:
  - `ip addr` : we can see ip address and ethernets interface. 
  - `ip netns list` : list of named network namespaces that have been created.


#### How Pod to Pod communication works on Node?
- Node has eth0.
- Communication happens via root namespace; rootns.
- rootns in node has veth1 and veth2.
- pods have their own eth0.
- pod1 eth0 pairs with veth1.
- pod2 eth0 pairs with veth2.
- there is a bridge between veth1 and veth2.
- bridge resolves the destination address using ARP table.

```
             NODE
       Root Network Namespace

              eth0
               |
          Linux Bridge
           /       \
        veth1      veth2
          |          |
          |          |
       Pod 1       Pod 2
       eth0        eth0
     10.0.0.2    10.0.0.3
```

- The bridge uses its forwarding database (FDB) to determine the destination MAC address. ARP helps resolve IPv4 IP-to-MAC mappings.
- Important: Not every CNI uses a Linux bridge. Calico and Cilium can use different networking architectures.

#### important points
- CNI: Configures Pod networking and IP connectivity. adds link entries to ip addr to connect to calico. 
- Kube-proxy: Implements Service networking using iptables, IPVS or nftables modes
- CoreDNS: Provides DNS-based service discovery.

#### later topics
- Network policy

