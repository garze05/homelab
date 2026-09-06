# ADR 0003: Use AdGuard Home for Private DNS

## Status

Accepted

## Context

Private services need stable names when host IP addresses change. Publishing private Tailscale or RFC 1918 addresses in public DNS creates unnecessary coupling and complicates migrations.

## Decision

Use AdGuard Home for the private namespace `lab.gabrielro.com`. Public Porkbun DNS remains separate.

```text
*.lab.gabrielro.com     → docker01
pve01.lab.gabrielro.com → pve01
```

## Reasons

- Separates public and private infrastructure.
- Allows internal IP changes without public DNS changes.
- Supports DNS filtering.
- Integrates with Tailscale Split DNS.

## Failure Behavior

If AdGuard Home fails, private names may stop resolving. Direct IP access remains the recovery mechanism.
