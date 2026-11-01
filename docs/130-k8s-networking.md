- Docker was used to be bundled into k8s core. This is heavy.
- But then OCI came: open controller initiative. Plug and play any container runtime.
- OCI runtime spec: image spec, runtime spec, distribution.
- 1.24 k8s removed docker from core.
- Docker re-architected to become OCI complaint.

- When we run `docker run ...` the request goes to docker daemon.
- Daemon does not start container.
- containerD also does not start it. it manages lifecycle; keeps a check.
- Docker shim: maintains connectivity between containers and containerD.
- runc : this is complaint to OCI initiative. starts the container. exits
- that is why we can even now run webassembly workloads on k8s.

- k8s worker node has kublet and CRI - Container runtime interface. CRI is also a specification
- CRI helps kublet talk to Container runtime.
- ContainerD is a container runtime. CRI implementation.
- user -> Kubectl API -> API server -> scheduler [add node] -> API server -> kubelet -> call CRI -> Container D -> shim -> runC
- now things go to CNI to attach the network interface. CNI is a specification.
- no default networking implementation. instead we have to opt one CNI implementation: eg: Calico, Cilium etc.
- 
