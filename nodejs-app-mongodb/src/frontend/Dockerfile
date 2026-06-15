# Durantic demo frontend — Kubernetes image.
# Serves the static UI and reverse-proxies /api/* to the backend Service.
# Config (env): BACKEND_URL (e.g. http://backend:3000), PORT (default 8080).
#
# Build (manual, no CI):
#   tar -czh -C nodejs-app-mongodb/src/frontend . \
#     | docker.exe build -t ghcr.io/dev1l/argocd-example-nodejs-app-mongodb:frontend -
#   docker.exe push ghcr.io/dev1l/argocd-example-nodejs-app-mongodb:frontend
FROM node:20-alpine

WORKDIR /app

# Production deps first for layer caching.
COPY package.json package-lock.json ./
RUN npm install --omit=dev

COPY server.js ./
COPY public ./public

ENV PORT=8080
EXPOSE 8080
USER node
CMD ["node", "server.js"]
