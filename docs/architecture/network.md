# Network Architecture

## Current Network

The current temporary network uses the ISP router and subnet `192.168.1.0/24`.

| Device | Address | Role |
| --- | --- | --- |
| ISP Router | `192.168.1.1` | Default gateway |
| pve01 | `192.168.1.53` | Proxmox hypervisor |
| docker01 | `192.168.1.55` | Debian Docker host |

## Docker Network

The shared reverse-proxy Docker network is `proxy-net`, currently using `172.18.0.0/16`. Homepage, Portainer, and Uptime Kuma connect to it so Nginx Proxy Manager can use Docker DNS names rather than host IP addresses.

```text
Nginx Proxy Manager
  ├─ http://uptime-kuma:3001
  ├─ https://portainer:9443
  └─ http://homepage:3000
```

Changing the IP address of `docker01` therefore does not require changes to these internal upstreams.

## DNS

AdGuard Home provides private DNS for `lab.gabrielro.com`.

```text
*.lab.gabrielro.com     → 192.168.1.55
pve01.lab.gabrielro.com → 192.168.1.53
```

Specific rewrites take precedence over the application wildcard. Public DNS is managed through Porkbun. Private infrastructure addresses must not be published in public DNS; Porkbun is also used for DNS-01 certificate validation.

## Tailscale

Tailscale runs natively on `docker01`, which currently advertises the subnet route `192.168.1.0/24`. Remote Tailscale clients can therefore reach devices such as Proxmox at `192.168.1.53:8006`.

Split DNS sends requests for `lab.gabrielro.com` to AdGuard Home.

## Known Limitation

`192.168.1.0/24` is common on residential networks. A remote client on the same local subnet can encounter route conflicts because its local route may be preferred over the Tailscale subnet route.

## Planned Network

After MikroTik deployment, the network is planned to use dedicated VLANs and less-common subnets:

| VLAN | Name | Subnet | Purpose |
| ---: | --- | --- | --- |
| 10 | Family | `10.10.10.0/24` | Trusted user devices |
| 20 | Tenants | `10.10.20.0/24` | Internet-only tenant devices |
| 30 | Homelab | `10.10.30.0/24` | Application servers |
| 40 | IoT | `10.10.40.0/24` | IoT devices |
| 50 | CCTV | `10.10.50.0/24` | Cameras and NVR |
| 99 | Management | `10.10.99.0/24` | Infrastructure management |

Expected placement: `pve01` on VLAN 99 and `docker01` on VLAN 30. Tailscale will then advertise only the needed networks, expected to include `10.10.30.0/24` and `10.10.99.0/24`.
