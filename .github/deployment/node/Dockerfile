FROM node:22-bookworm-slim

# Set working directory to /app (monorepo root inside container)
WORKDIR /app

# Install OS dependencies and Yarn via Corepack
RUN apt-get update && \
    apt-get install -y --no-install-recommends curl && \
    corepack enable && corepack prepare yarn@stable --activate && \
    rm -rf /var/lib/apt/lists/*

# Copy monorepo root files (package.json, yarn.lock, and yarn config)
COPY ./package.json ./yarn.lock ./

# Copy root package files first
COPY package.json yarn.lock ./

# Copy blocksuite folder (important!)
COPY blocksuite ./blocksuite
COPY tools ./tools 
# Copy other folders
COPY packages ./packages

# Install dependencies for all workspaces from root
RUN yarn install

# Build frontend apps using workspace commands
RUN yarn workspace apps/web build
RUN yarn workspace admin build
RUN yarn workspace apps/mobile build

# Prepare backend directory and copy backend source
RUN mkdir -p /app/backend
RUN cp -r packages/backend/server/* /app/backend/

# Change working directory to backend for installing backend-specific dependencies
WORKDIR /app/backend

# Install backend dependencies
RUN yarn install --immutable

# Copy built frontend assets to backend static folder
RUN mkdir -p static static/admin static/mobile && \
    cp -r ../packages/frontend/apps/web/dist static/ && \
    cp -r ../packages/frontend/admin/dist static/admin/ && \
    cp -r ../packages/frontend/apps/mobile/dist static/mobile/

# Expose backend port if needed (adjust port accordingly)
# EXPOSE 3000

# Start backend server
CMD ["node", "./dist/main.js"]
