# Durantic demo backend — Kubernetes image.
# Renders submitted text into a PDF (random standard font) and stores it in MongoDB.
# Config (env): MONGODB_URL, MONGODB_DB (default durantic_demo), PORT (default 3000).
#
# Build (manual, no CI):
#   tar -czh -C nodejs-app-mongodb/src/backend . \
#     | docker.exe build -t ghcr.io/dev1l/argocd-example-nodejs-app-mongodb:backend -
#   docker.exe push ghcr.io/dev1l/argocd-example-nodejs-app-mongodb:backend
FROM node:20-alpine

WORKDIR /app

# Production deps first for layer caching.
COPY package.json package-lock.json ./
RUN npm install --omit=dev

COPY server.js ./

ENV PORT=3000
EXPOSE 3000
USER node
CMD ["node", "server.js"]
