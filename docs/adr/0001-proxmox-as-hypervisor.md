# ADR 0001: Use Proxmox VE as the Hypervisor

## Status

Accepted

## Context

The homelab requires virtual machines, future storage workloads, networking experiments, and potentially multiple nodes.

## Decision

Use Proxmox VE as the primary hypervisor. The hypervisor remains minimal and does not directly host general workloads such as Docker.

## Consequences

### Positive

- Clear separation between infrastructure and workloads.
- Native VM and LXC support.
- Snapshot and backup capabilities.
- Future clustering and high-availability support.
- Flexible networking.

### Negative

- Additional abstraction compared with installing Linux directly.
- Separate planning is required for VM storage and backups.
