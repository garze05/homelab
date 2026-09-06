# Architecture Overview

## Purpose

The homelab is a platform for learning and operating virtualization, Linux administration, Docker, networking, DNS, reverse proxies, TLS, VPN, monitoring, backup and disaster recovery, and Infrastructure as Code.

## Physical Infrastructure

### pve01

`pve01` is a Dell OptiPlex 7060 with 8 GB DDR4 RAM and a 256 GB SATA SSD. It runs Proxmox VE and has the current management IP `192.168.1.53`.

The Proxmox host should remain minimal. General application workloads are run outside the hypervisor.

## Virtual Infrastructure

### docker01

`docker01` is a Debian 13 virtual machine with the current IP `192.168.1.55`. It is the primary Docker application host and currently has:

- 2 vCPUs
- 4 GB RAM
- 64 GB disk

Tailscale runs directly on Debian so remote administration remains available if Docker fails.

## Application Layer

Docker applications currently include:

- Nginx Proxy Manager
- AdGuard Home
- Uptime Kuma
- Portainer
- Homepage

## Configuration and State

Docker Compose definitions are versioned in this repository and are deployed under `/opt/homelab/docker/`. Persistent application state is stored separately under `/srv/docker/`.

For example:

```text
/opt/homelab/docker/uptime-kuma/compose.yml
/srv/docker/uptime-kuma/data/
```

This separation keeps definitions reproducible and lets application data be backed up independently.

## Failure Isolation

The architecture is designed to reduce cascading failures:

```text
Nginx Proxy Manager failure
  ├─ Friendly application URLs are unavailable
  ├─ Proxmox direct access remains available
  ├─ SSH remains available
  └─ Tailscale remains available

Docker failure
  ├─ Docker applications are unavailable
  ├─ Debian remains accessible
  ├─ Native Tailscale remains available
  └─ Proxmox remains available

AdGuard Home failure
  ├─ Private DNS names may fail
  ├─ Direct IP access remains possible
  └─ docker01 does not depend exclusively on AdGuard Home for DNS
```
