FROM node:22-bookworm-slim

WORKDIR /app

RUN apt-get update && \
    apt-get install -y --no-install-recommends openssl && \
    rm -rf /var/lib/apt/lists/*

# Copy backend source
COPY ./packages/backend/server /app

# Copy frontend monorepo root (with package.json and workspaces config)
COPY ./packages/frontend /frontend

# Install all frontend dependencies at frontend root (monorepo context)
RUN cd /frontend && npm install

# Build each frontend app inside the monorepo
RUN cd /frontend/apps/web && npm run build && \
    cd ../../admin && npm run build && \
    cd ../apps/mobile && npm run build

# Copy built dist folders to backend static folder
RUN mkdir -p /app/static /app/static/admin /app/static/mobile && \
    cp -r /frontend/apps/web/dist /app/static && \
    cp -r /frontend/admin/dist /app/static/admin && \
    cp -r /frontend/apps/mobile/dist /app/static/mobile

# Install backend dependencies if needed
RUN cd /app && npm install

CMD ["node", "./dist/main.js"]
