# syntax=docker/dockerfile:1-labs

## This dockerfile builds the client entirely in a Docker context

# slim image can't be used since we need git to fetch the commit hash
# FROM node:lts-slim AS build
FROM arm64v8/node:lts-slim AS build

# Set build arguments
ARG IS_STABLE=0
ARG COMMIT_HASH
# Set environment variables
ENV IS_STABLE=$IS_STABLE
ENV COMMIT_HASH=$COMMIT_HASH

COPY package.json package-lock.json .npmrc /app/
COPY frontend /app/frontend
COPY packages /app/packages
WORKDIR /app/frontend

# Build client
RUN --security=insecure npm ci --no-audit && npm run build

# Deploy built distribution to nginx
FROM arm64v8/nginx:stable-alpine-slim

ENV ASSETS=/usr/share/nginx/html
ENV NGINX_DEFAULT_SITE_CONFIG=/etc/nginx/conf.d/default.conf

COPY packaging/docker/contents/nginx.conf "$NGINX_DEFAULT_SITE_CONFIG"
COPY packaging/docker/contents/*.sh /
COPY LICENSE /usr/share/licenses/jellyfin-vue

RUN --security=insecure rm -rf "$ASSETS"/*
COPY --from=build /app/frontend/dist/ "$ASSETS"
RUN --security=insecure chmod +x /*.sh && /postunpack.sh && rm /postunpack.sh
WORKDIR "$ASSETS"

EXPOSE 80

# Set labels
LABEL maintainer="Jellyfin Packaging Team - packaging@jellyfin.org"
LABEL org.opencontainers.image.source="https://github.com/jellyfin/jellyfin-vue"
