# ADR 0002: Run Docker Inside a Debian VM

## Status

Accepted

## Context

Existing applications were deployed using Docker Compose. Considered models were Docker directly on Proxmox, Docker inside LXC, individual LXC containers, and Docker in a dedicated VM.

## Decision

Run Docker inside a Debian 13 VM named `docker01`.

## Reasons

- Keeps the Proxmox host clean.
- Provides kernel isolation from the hypervisor.
- Supports straightforward migration of Docker Compose applications.
- Is easier to troubleshoot than nested Docker in LXC.
- Provides a clear target for future Ansible configuration.
- Lets the application host be backed up, restored, or migrated as one unit.

## Consequences

The VM introduces more memory overhead than LXC, but operational simplicity and isolation are more valuable for the primary application host.
