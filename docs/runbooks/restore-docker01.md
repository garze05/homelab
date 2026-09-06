# docker01 Recovery Runbook

## Objective

Recover the Docker application host after VM corruption or replacement.

## Requirements

- Debian 13 VM
- Docker Engine and Docker Compose plugin
- Git access
- A backup of `/srv/docker`
- Tailscale access or local console

## Base VM

Expected hostname: `docker01`.

Expected packages:

```text
docker-ce
docker-ce-cli
containerd.io
docker-buildx-plugin
docker-compose-plugin
git
curl
qemu-guest-agent
```

## Restore the Repository

```bash
sudo mkdir -p /opt/homelab
sudo chown garze:garze /opt/homelab
cd /opt/homelab
git clone <repository> .
```

## Restore Persistent State

Restore application data under `/srv/docker/`. Expected directories include:

```text
/srv/docker/adguardhome
/srv/docker/nginx-proxy-manager
/srv/docker/uptime-kuma
/srv/docker/portainer
/srv/docker/homepage
```

## Create the Docker Network

```bash
docker network create proxy-net
```

If it already exists, verify it with `docker network inspect proxy-net`.

## Start Services

Recommended order:

1. AdGuard Home
2. Nginx Proxy Manager
3. Uptime Kuma
4. Portainer
5. Homepage

For each service:

```bash
cd /opt/homelab/docker/<service>
docker compose up -d
```

## Validate

```bash
dig @127.0.0.1 status.lab.gabrielro.com
dig @192.168.1.55 status.lab.gabrielro.com
docker ps
tailscale status
```

Also verify `https://npm.lab.gabrielro.com` and `https://status.lab.gabrielro.com`. Required containers should report `Up`.

Restore Tailscale's advertised route if necessary.

## Important

Do not restore secrets or application databases from Git. Git holds declarative configuration; persistent state must be restored from backups.
