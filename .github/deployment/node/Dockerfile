FROM node:22-bookworm-slim

WORKDIR /app

# Install openssl and clean up
RUN apt-get update && \
    apt-get install -y --no-install-recommends openssl && \
    rm -rf /var/lib/apt/lists/*

# Copy backend source code
COPY ./packages/backend/server /app

# Copy frontend monorepo root (assuming package.json and workspaces config are inside frontend folder)
COPY ./packages/frontend /frontend

# Upgrade npm to latest to ensure workspace and modern package support
RUN npm install -g npm@latest

# Install frontend dependencies in the monorepo context
RUN cd /frontend && npm install

# Build each frontend app inside the monorepo
RUN cd /frontend/apps/web && npm run build && \
    cd ../../admin && npm run build && \
    cd ../apps/mobile && npm run build

# Create backend static folders and copy frontend build artifacts
RUN mkdir -p /app/static/web /app/static/admin /app/static/mobile && \
    cp -r /frontend/apps/web/dist /app/static/web && \
    cp -r /frontend/admin/dist /app/static/admin && \
    cp -r /frontend/apps/mobile/dist /app/static/mobile

# Install backend dependencies
RUN cd /app && npm install

# Run the backend server
CMD ["node", "./dist/main.js"]
