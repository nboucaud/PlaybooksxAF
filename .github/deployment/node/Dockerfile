# syntax=docker/dockerfile:1.4
FROM node:22-bookworm-slim AS base

WORKDIR /app

RUN apt-get update && \
    apt-get install -y --no-install-recommends curl && \
    rm -rf /var/lib/apt/lists/*

# Copy root files for all stages
COPY package.json yarn.lock ./

COPY blocksuite ./blocksuite
COPY tools ./tools
COPY packages ./packages

# ========================
# Install dependencies stage
FROM base AS deps

RUN npm ci

# ========================
# Build frontend apps stage
FROM deps AS frontend-build

RUN npm run --workspace=apps/web build
RUN npm run --workspace=admin build
RUN npm run --workspace=apps/mobile build

# ========================
# Backend preparation and build stage
FROM deps AS backend-build

WORKDIR /app/backend

# Copy backend source from packages to backend dir
RUN mkdir -p /app/backend
RUN cp -r ../packages/backend/server/* /app/backend/

# Install backend deps strictly in backend folder
RUN npm ci

# Copy frontend builds from frontend-build stage
COPY --from=frontend-build /app/packages/frontend/apps/web/dist /app/backend/static/
COPY --from=frontend-build /app/packages/frontend/admin/dist /app/backend/static/admin/
COPY --from=frontend-build /app/packages/frontend/apps/mobile/dist /app/backend/static/mobile/

# ========================
# Final image stage
FROM node:22-bookworm-slim AS runtime

WORKDIR /app/backend

# Copy backend build from previous stage
COPY --from=backend-build /app/backend /app/backend

# Install only production deps for backend
RUN npm ci --production

# Expose backend port (adjust if necessary)
# EXPOSE 3000

CMD ["node", "./dist/main.js"]
