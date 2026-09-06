# Gabriel's Homelab

A self administrated homelab focused on virtualization, networking, self-hosting, infrastructure as code, observability, security, automation. data privacy and disaster recovery.

The environment is designed around separation of responsibilities,
network segmentation, reproducibility and minimizing unnecessary
dependencies between infrastructure components.

## Goals

- Build a scalable, resilient, and disaster-recovery-oriented homelab following security, reliability, and infrastructure best practices.
- Design the environment with High Availability in mind, allowing services and workloads to be migrated, restored, or recreated with minimal manual intervention.
- Achieve reproducibility through Infrastructure as Code, configuration management, version-controlled infrastructure definitions, and documented recovery procedures.
- Host self-managed applications, internal services, and automation workloads that can run continuously and reliably.
- Provision Linux and Windows virtual machines on demand using Infrastructure as Code, with the ability to automatically create, configure, use, and destroy temporary environments when needed.
- Provide isolated environments for software development, code testing, production-like simulations, malware-safe analysis workflows, and security analysis of binaries and applications.
- Separate public, private, management, IoT, CCTV, family, and other network workloads through network segmentation, VLANs, firewall policies, and least-privilege access controls.
- Keep administrative and private services inaccessible from the public Internet and expose them only through trusted private connectivity such as Tailscale.
- Provide secure remote access to internal infrastructure without directly exposing management interfaces such as Proxmox, routers, or internal dashboards to the Internet.
- Publish selected public services through a cloud VPS acting as the public ingress point, using a private WireGuard tunnel between the VPS and the homelab instead of relying on inbound port forwarding on the residential router.
- Separate public DNS from private DNS, using an internal namespace for homelab services and split DNS for remote private access.
- Centralize HTTPS and certificate management while keeping internal service-to-service communication simple and appropriately isolated.
- Minimize coupling between components so that the failure of DNS, Docker, reverse proxy, VPN, or an individual application does not unnecessarily prevent access to unrelated infrastructure.
- Implement reliable backup and restore strategies for virtual machines, application state, configuration, and critical data, including future off-site backups.
- Build centralized monitoring, alerting, metrics, and logging capabilities to improve observability and simplify troubleshooting.
- Maintain clear architecture documentation, Architecture Decision Records (ADRs), and operational runbooks so the environment can be understood, maintained, and recovered without relying on undocumented manual knowledge.
- Use the homelab as a practical learning platform for Proxmox, Linux administration, Docker, networking, VPNs, DNS, reverse proxies, TLS, Infrastructure as Code, configuration management, storage, observability, cybersecurity, and High Availability.

## Current Status

### Infrastructure

- Dell OptiPlex 7060 with 8gb DDR4 Ram, 256 GB SATA SSD
- Proxmox VE as main OS (`pve01`).
- Debian 13 VM to run Docker Engine + Docker Compose (`docker01`)
- Tailscale remote access
- AdGuard Home private DNS
- Nginx Proxy Manager reverse proxy
- Let's Encrypt wildcard certificates
- Uptime Kuma monitoring
- Portainer
- Homepage

## Current Architecture

```text
Internet
   |
ISP Router
   |
192.168.1.0/24
   |
   +-- pve01
   |     192.168.1.53
   |     Proxmox VE
   |
   +-- docker01
         192.168.1.55
         Debian 13
         |
         +-- Tailscale (native)
         |
         +-- Docker
              |
              +-- Nginx Proxy Manager
              +-- AdGuard Home
              +-- Uptime Kuma
              +-- Portainer
              +-- Homepage
```

## Private Domain

Internal services use:

```text
lab.gabrielro.com
```

Examples:

```text
pve01.lab.gabrielro.com
status.lab.gabrielro.com
npm.lab.gabrielro.com
portainer.lab.gabrielro.com
home.lab.gabrielro.com
```

The `lab.gabrielro.com` namespace is private and resolved internally
through AdGuard Home.

Public DNS is managed separately through Porkbun.

## TLS

Internal web applications are served through Nginx Proxy Manager using
a Let's Encrypt DNS-01 wildcard certificate covering:

```text
lab.gabrielro.com
*.lab.gabrielro.com
```

## Remote Access

Remote access is provided through Tailscale.

`docker01` currently acts as a subnet router for:

```text
192.168.1.0/24
```

Tailscale Split DNS forwards queries for `lab.gabrielro.com` to AdGuard Home. No Proxmox management ports are intentionally exposed directly to the
public Internet.

## Design Principles

- Keep the Proxmox host minimal.
- Run applications outside the hypervisor.
- Separate application configuration from persistent state.
- Prefer private access for administrative interfaces.
- Use private DNS instead of hard-coded application URLs.
- Minimize coupling between infrastructure components.
- Maintain recovery paths if Docker, DNS or reverse proxy services fail.
- Store declarative configuration in Git.
- Keep secrets and application state outside Git.
- Build toward Infrastructure as Code.
- Design for future network segmentation and high availability.

## Roadmap

- [x] Install Proxmox VE
- [x] Create Debian Docker host
- [x] Migrate Nginx Proxy Manager
- [x] Migrate AdGuard Home
- [x] Migrate Uptime Kuma
- [x] Deploy Portainer
- [x] Deploy Homepage
- [x] Configure private .lab DNS
- [x] Configure wildcard TLS certificate
- [x] Configure Tailscale remote access
- [x] Configure Tailscale subnet routing
- [x] Configure Split DNS
- [ ] Create validated Proxmox backup
- [ ] Deploy MikroTik router
- [ ] Implement VLAN segmentation
- [ ] Upgrade Proxmox host memory
- [ ] Design NAS/storage architecture
- [ ] Implement automated backups
- [ ] Introduce Ansible
- [ ] Introduce Terraform
- [ ] Add centralized observability
- [ ] Add additional Proxmox nodes
