# Tailscale Remote Access Runbook

## Purpose

Verify or restore remote access to the homelab.

## Verify Tailscale

On `docker01`:

```bash
tailscale status
tailscale ip -4
```

## Verify IP Forwarding

```bash
sysctl net.ipv4.ip_forward
```

Expected output:

```text
net.ipv4.ip_forward = 1
```

## Current Subnet Route

`docker01` should advertise `192.168.1.0/24`:

```bash
sudo tailscale set --advertise-routes=192.168.1.0/24 --accept-dns=false
```

The route must also be approved in the Tailscale admin console.

## Test Proxmox

From a remote Tailscale client:

```bash
ping 192.168.1.53
nc -vz 192.168.1.53 8006
```

Open `https://192.168.1.53:8006` in a browser.

## Private DNS

Split DNS should send `lab.gabrielro.com` queries to AdGuard Home through `docker01`:

```bash
dig pve01.lab.gabrielro.com
```

Expected answer: `192.168.1.53`.

## Known Issue

Remote networks using `192.168.1.0/24` can conflict with the current homelab subnet. Use a non-conflicting network or direct Tailscale IPs as a temporary workaround. The planned resolution is migration to dedicated `10.10.x.0/24` VLANs.
