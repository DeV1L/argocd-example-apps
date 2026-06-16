# nodejs-app-mongodb

A three-tier **Node.js + MongoDB** demo deployed to Kubernetes by ArgoCD. It is the
Kubernetes counterpart of the bare-metal Durantic example
(`durantic-tf-examples/nodejs-app-mongodb`): same app, delivered via GitOps to a
single-node k3s cluster.

The provisioning Terraform lives in `durantic-tf-examples/k3s-standalone-argocd` — it
turns a Durantic machine into a single-node k3s + ArgoCD cluster and points ArgoCD at the
`apps/` directory of this repo.

## Tiers

| Tier | Image | Service | Notes |
|------|-------|---------|-------|
| frontend | `ghcr.io/dev1l/argocd-example-nodejs-app-mongodb:frontend` | Ingress `/` (Traefik) → `:80→8080` | Serves the UI, reverse-proxies `/api/*` to `backend` |
| backend | `ghcr.io/dev1l/argocd-example-nodejs-app-mongodb:backend` | ClusterIP `:3000` | Renders text → PDF, stores it in MongoDB |
| mongodb | `mongo:8.0` (upstream) | headless `:27017` | StatefulSet + `local-path` PVC (1Gi) |

Service discovery is pure Kubernetes DNS: the frontend talks to `http://backend:3000`,
the backend talks to `mongodb://…@mongodb:27017`.

## Layout

Everything for this example lives under this one folder:

```
nodejs-app-mongodb/
├── apps/                # app-of-apps target (ARGOCD_APP_PATH = nodejs-app-mongodb/apps)
│   ├── nodejs-app-mongodb.yaml   # Application → nodejs-app-mongodb/manifests
│   └── argocd-extras.yaml        # Application → nodejs-app-mongodb/argocd-extras
├── manifests/           # the app ArgoCD deploys (namespace nodejs-app-mongodb)
│   ├── mongodb.yaml
│   ├── backend.yaml
│   └── frontend.yaml
├── argocd-extras/       # NodePort Service exposing the ArgoCD UI (:30080)
│   └── argocd-server-nodeport.yaml
└── src/                 # build contexts for the two custom images (not synced by ArgoCD)
    ├── frontend/        # server.js, package.json, public/, Dockerfile
    └── backend/         # server.js, package.json, Dockerfile
```

The cluster bootstrap points ArgoCD at `nodejs-app-mongodb/apps` (app-of-apps).

## Building the images (manual — no CI)

```bash
cd nodejs-app-mongodb
echo "$GHCR_TOKEN" | docker login ghcr.io -u dev1l --password-stdin
for tier in frontend backend; do
  tar -czh -C src/$tier . \
    | docker build -t ghcr.io/dev1l/argocd-example-nodejs-app-mongodb:$tier -
  docker push ghcr.io/dev1l/argocd-example-nodejs-app-mongodb:$tier
done
```

Images are private; the k3s node authenticates to ghcr.io via
`/etc/rancher/k3s/registries.yaml` (configured by the provisioning role), so no
`imagePullSecret` is needed in the manifests.

## Verify

```bash
# Frontend UI + the full frontend → backend → mongo path:
curl http://<node-public-ip>/
curl -X POST http://<node-public-ip>/api/generate \
  -H 'content-type: application/json' -d '{"text":"hello durantic"}'
curl http://<node-public-ip>/api/documents
```
