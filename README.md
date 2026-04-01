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


#### Verify

```bash
# Each request shows a different pod hostname (2 replicas)
curl http://<GATEWAY_PUBLIC_IP>
```
