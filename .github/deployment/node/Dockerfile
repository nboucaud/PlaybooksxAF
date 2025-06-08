FROM node:22-bookworm-slim

WORKDIR /app

# Install openssl first (for backend use)
RUN apt-get update && \
    apt-get install -y --no-install-recommends openssl && \
    rm -rf /var/lib/apt/lists/*

# Copy backend source
COPY ./packages/backend/server /app

# Copy frontend source (not dist) for building inside container
COPY ./packages/frontend/apps/web /frontend/apps/web
COPY ./packages/frontend/admin /frontend/admin
COPY ./packages/frontend/apps/mobile /frontend/apps/mobile

# Build frontend apps to generate dist folders
RUN cd /frontend/apps/web && npm install && npm run build && \
    cd ../../admin && npm install && npm run build && \
    cd ../apps/mobile && npm install && npm run build

# Create target directories and copy built dist folders to /app/static
RUN mkdir -p /app/static /app/static/admin /app/static/mobile && \
    cp -r /frontend/apps/web/dist /app/static && \
    cp -r /frontend/admin/dist /app/static/admin && \
    cp -r /frontend/apps/mobile/dist /app/static/mobile

# Optionally, install backend dependencies if needed
RUN cd /app && npm install

# Set the command to start backend server
CMD ["node", "./dist/main.js"]
