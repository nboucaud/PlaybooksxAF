FROM node:22-bookworm-slim

# Set working directory to /app (monorepo root inside container)
WORKDIR /app

# Install OS dependencies and Yarn via Corepack
RUN apt-get update && \
    apt-get install -y --no-install-recommends curl && \
    corepack enable && corepack prepare yarn@stable --activate && \
    rm -rf /var/lib/apt/lists/*

# Copy entire monorepo root (adjust path as needed)
# Assume your repo has this structure:
# /package.json (with workspaces)
# /packages/frontend
# /packages/backend/server
# /packages/common/env

COPY ./package.json ./yarn.lock ./  # copy root package files
COPY ./packages ./packages           # copy all packages including frontend, backend, common

# Install dependencies for all workspaces from root
RUN yarn install

# Build all frontend apps (adjust script names if needed)
RUN yarn workspace apps/web build
RUN yarn workspace admin build
RUN yarn workspace apps/mobile build

# Copy backend source to /app/backend for clarity (optional)
RUN mkdir -p /app/backend
RUN cp -r packages/backend/server/* /app/backend/

# Install backend dependencies (if backend uses separate dependencies)
WORKDIR /app/backend
RUN yarn install

# Copy built frontend assets to backend static folder
RUN mkdir -p /app/backend/static /app/backend/static/admin /app/backend/static/mobile && \
    cp -r ../packages/frontend/apps/web/dist /app/backend/static && \
    cp -r ../packages/frontend/admin/dist /app/backend/static/admin && \
    cp -r ../packages/frontend/apps/mobile/dist /app/backend/static/mobile

# Set workdir for running backend
WORKDIR /app/backend

# Start backend server
CMD ["node", "./dist/main.js"]
