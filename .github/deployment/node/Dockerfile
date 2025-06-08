FROM node:22-bookworm-slim

# Set working directory for backend
WORKDIR /app

# Install OS dependencies and Yarn via Corepack
RUN apt-get update && \
    apt-get install -y --no-install-recommends curl && \
    corepack enable && corepack prepare yarn@stable --activate && \
    rm -rf /var/lib/apt/lists/*

# Copy backend source code
COPY ./packages/backend/server /app

# Copy frontend monorepo (root includes package.json and workspace config)
COPY ./packages/frontend /frontend

# Set working directory to frontend monorepo root
WORKDIR /frontend

COPY env ./env
# Install all dependencies via Yarn (resolves workspaces correctly)
RUN yarn install

# Build all apps via yarn scripts
RUN yarn build:all

# Copy built frontend assets to backend static folder
RUN mkdir -p /app/static /app/static/admin /app/static/mobile && \
    cp -r apps/web/dist /app/static && \
    cp -r admin/dist /app/static/admin && \
    cp -r apps/mobile/dist /app/static/mobile

# Switch back to backend dir and install backend dependencies
WORKDIR /app
RUN yarn install

# Run the backend
CMD ["node", "./dist/main.js"]


