# ArgoCD Example Apps

Example applications for use with ArgoCD for demos.

## Apps

### whoami

A simple HTTP server that returns hostname, IP, and request headers. Useful for verifying load balancing and failover across K8S nodes.

- **Image**: [traefik/whoami](https://hub.docker.com/r/traefik/whoami)
- **Replicas**: 2
- **Ingress**: Catch-all rule on `/` with `ingressClassName: traefik`

### whoami-cilium

Same as `whoami` but configured for clusters using Cilium Ingress.

- **Image**: [traefik/whoami](https://hub.docker.com/r/traefik/whoami)
- **Replicas**: 2
- **Ingress**: Catch-all rule on `/` with `ingressClassName: cilium`

### whoami-nginx

Same as `whoami` but configured for clusters using NGINX Ingress Controller.

- **Image**: [traefik/whoami](https://hub.docker.com/r/traefik/whoami)
- **Replicas**: 2
- **Ingress**: Catch-all rule on `/` with `ingressClassName: nginx`

### whoami-gateway

Same as `whoami` but exposed through Gateway API instead of Ingress. Built for the [k8s-baremetal-lab](https://github.com/DeV1L/k8s-baremetal-lab) cluster: the `HTTPRoute` attaches to a `Gateway` named `lab-gateway` in the `default` namespace and serves `whoami.lab.local`.

- **Image**: [traefik/whoami](https://hub.docker.com/r/traefik/whoami)
- **Replicas**: 2
- **Routing**: `HTTPRoute` for `whoami.lab.local` -> `lab-gateway` (`default` namespace)

### nfs-shared-demo

A writer and a reader Deployment sharing one `ReadWriteMany` PVC on the `nfs-csi` StorageClass. Pod anti-affinity keeps them on different nodes, so the reader tails lines the writer appends from another machine.

- **Image**: busybox
- **Storage**: `ReadWriteMany` PVC `shared-log` on `nfs-csi`
- **Verify**: `kubectl -n <namespace> logs deploy/reader --tail=5`


#### Verify

```bash
# Each request shows a different pod hostname (2 replicas)
curl http://<GATEWAY_PUBLIC_IP>
```
