# Nginx Proxy Manager Recovery Runbook

## Objective

Restore Nginx Proxy Manager (NPM) after a container, host, or persistent-state failure.

## Prerequisites

- Docker and Docker Compose are available on `docker01`.
- The external Docker network `proxy-net` exists.
- A backup of `/srv/docker/nginx-proxy-manager/` is available when state must be recovered.

## Restore State

Restore the NPM data and certificate directories to:

```text
/srv/docker/nginx-proxy-manager/data
/srv/docker/nginx-proxy-manager/letsencrypt
```

These directories contain NPM configuration, proxy-host definitions, and certificate state. Do not store them in Git.

## Start NPM

```bash
cd /opt/homelab/docker/nginx-proxy-manager
docker network inspect proxy-net
docker compose up -d
docker compose ps
```

## Validate

1. Confirm the container is running and ports 80, 81, and 443 are bound.
2. Open `https://npm.lab.gabrielro.com`.
3. Confirm proxy hosts can reach their Docker service names through `proxy-net`.
4. Confirm an internal application URL, for example `https://status.lab.gabrielro.com`, serves a valid certificate.

## Failure Notes

If NPM is unavailable, friendly application URLs will fail. Direct Proxmox access, SSH, and native Tailscale access should remain available for recovery.
