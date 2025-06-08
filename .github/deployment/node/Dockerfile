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
#RUN npm install -g npm@latest

# Install frontend dependencies in the monorepo context
#RUN cd /frontend && npm install

# Build each frontend app inside the monorepo
# For each frontend app, install dependencies and build separately
RUN cd /frontend/apps/web && npm install && npm run build && \
    cd ../../admin && npm install && npm run build && \
    cd ../apps/mobile && npm install && npm run build

# Copy built dist folders to backend static folder
RUN mkdir -p /app/static /app/static/admin /app/static/mobile && \
    cp -r /frontend/apps/web/dist /app/static && \
    cp -r /frontend/admin/dist /app/static/admin && \
    cp -r /frontend/apps/mobile/dist /app/static/mobile

# Install backend dependencies if needed
RUN cd /app && npm install

CMD ["node", "./dist/main.js"]


