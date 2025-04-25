This Dockerfile must have it's context at the root of the repository. From this folder:

``
docker build ../.. -t your/tag -f Dockerfile
``

Alternatively, with the shell at the root of this repository:

``
docker build . -t your/tag -f packaging/docker/Dockerfile
``

NOTE XBO: edits to build on raspberrypi/arm64v8

Dockerfile:
- change base image to arm64v8 equivalents
- set dockerfile syntax to labs-1 (syntax=docker/dockerfile:1-labs)
- append --insecure flag to RUN commands (otherwise fails with exit code 159)
- Dont run as nginx user (permission denied for creating /run/nginx.pid)

Create buildx context that supports RUN --insecure flag
```sh
docker buildx create --use --name insecure-builder --buildkitd-flags '--allow-insecure-entitlement security.insecure'
docker buildx ls
docker buildx use <context name>
```

Build with insecure flag:
```sh
docker buildx build --allow security.insecure --load ../.. --platform=linux/arm64 -t local/jellyfin-vue:latest -f Dockerfile
```
