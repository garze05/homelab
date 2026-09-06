# ADR 0004: Use Tailscale for Private Remote Access

## Status

Accepted

## Context

Administrative interfaces such as Proxmox need remote access without exposing management ports directly to the public Internet.

## Decision

Use Tailscale for private remote access. It runs natively on `docker01`, which acts as a subnet router for `192.168.1.0/24`. Split DNS is configured for `lab.gabrielro.com`.

## Reasons

- No public port forwarding is required.
- Provides encrypted remote connectivity.
- Supports subnet routing and private DNS.
- Enables future access to management VLANs.

## Future

After MikroTik deployment, advertised routes will be updated for the required VLAN networks, expected to include `10.10.30.0/24` and `10.10.99.0/24`.
