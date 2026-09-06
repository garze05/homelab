# Services

## Nginx Proxy Manager

- Role: reverse proxy, internal HTTPS termination, and Let's Encrypt certificate management.
- Network: `proxy-net`.
- Persistent state: `/srv/docker/nginx-proxy-manager/`.
- Management hostname: `npm.lab.gabrielro.com`.

## AdGuard Home

- Role: private DNS, DNS rewrites, and advertisement/tracker filtering.
- Network mode: `host`.
- Persistent state: `/srv/docker/adguardhome/`.

AdGuard Home is intentionally not the mandatory DNS resolver for all of `docker01`, avoiding circular dependencies during DNS failures.

## Uptime Kuma

- Role: service availability monitoring.
- Persistent state: `/srv/docker/uptime-kuma/`.
- Hostname: `status.lab.gabrielro.com`.

Uptime Kuma uses AdGuard Home as its DNS resolver when it needs to resolve private service names. Infrastructure monitors can use direct IP addresses to reduce monitoring dependencies, such as AdGuard's web UI and Proxmox.

## Portainer

- Role: Docker administration UI.
- Hostname: `portainer.lab.gabrielro.com`.
- Docker socket access: `/var/run/docker.sock`.

Socket access grants significant control over the Docker daemon, so Portainer must remain a private administrative service.

## Homepage

- Role: homelab landing page and dashboard.
- Hostname: `home.lab.gabrielro.com`.
- Persistent configuration: `/srv/docker/homepage/config/`.

## Tailscale

Tailscale runs directly on Debian, not in Docker. This preserves remote access when Docker fails, supports subnet routing, and avoids coupling administration to the Docker daemon.
