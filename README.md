# ArgoCD Example Apps

Example applications for use with ArgoCD in Durantic K3s cluster demos.

## Apps

### whoami

A simple HTTP server that returns hostname, IP, and request headers. Useful for verifying load balancing and failover across K3s nodes.

- **Image**: [traefik/whoami](https://hub.docker.com/r/traefik/whoami)
- **Replicas**: 2
- **Ingress**: Catch-all rule on `/` with `ingressClassName: traefik`

#### Usage with Durantic

Set these account variables in the Durantic Dashboard:

| Variable | Value |
|----------|-------|
| `ARGOCD_REPO_URL` | `https://github.com/DeV1L/argocd-example-apps` |
| `ARGOCD_TARGET_REVISION` | `main` |
| `ARGOCD_APP_PATH` | `whoami` |

#### Verify

```bash
# Each request shows a different pod hostname (2 replicas)
curl http://<GATEWAY_PUBLIC_IP>
```
