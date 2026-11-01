- Docker was used to be bundled into k8s core. This is heavy.
- But then OCI came: open controller initiative. Plug and play any container runtime.
- OCI runtime spec: image spec, runtime spec, distribution.
- 1.24 k8s removed docker from core.
- Docker re-architected to become OCI complaint.

### Docker working
- When we run `docker run ...` the request goes to docker daemon.
- Daemon does not start container.
- containerD also does not start it. it manages lifecycle; keeps a check.
- Docker shim: maintains connectivity between containers and containerD.
- runc : this is complaint to OCI initiative. starts the container. exits
- that is why we can even now run webassembly workloads on k8s.


### k8s networking
- k8s worker node has kublet and CRI - Container runtime interface. CRI is also a specification.
- CRI helps kublet talk to Container runtime.
- ContainerD is a container runtime. CRI implementation.
- user -> Kubectl API -> API server -> scheduler [add node] -> API server -> kubelet -> call CRI -> Container D -> shim -> runC.
- now things go to CNI to attach the network interface. CNI is a specification.
- no default networking implementation. instead we have to opt one CNI implementation: eg: Calico, Cilium, flanel, weave etc.
- There should be something to attach IP address to pods; enable them to communicate across cluster etc.
- pause container gets created when spinning up a pod. Holds the network namespace. netns. ContainerD creates this network namesapce.
- all containers in a pod have same ip address.
- run `ip addr` on a pod: we can see ip address and ethernets interface. This ethernet will communicate with the virtual ethernet pair of the host.
- run `ip netns list` on a pod: list of network namespaces that have been created.
- 

#### How Pod to Pod communication works on Node?
- Node has eth0.
- Communication happens via root namespace; rootns.
- rootns in node has veth1 and veth2.
- pods have their own eth0.
- pod1 eth0 pairs with veth1.
- pod2 eth0 pairs with veth2.
- there is a bridge between veth1 and veth2.
- bridge resolves the destination address using ARP table.


#### important points
- CNI adds link entries to ip addr to connect to calico.
- Kube-proxy manages ip tables.
- CoreDNS acts as service discovery.

#### later topics
- Network policy

