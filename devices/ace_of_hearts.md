# ace-of-hearts

> **Type:** Physical Server | **IP:** 192.168.1.111 | **Network:** LAN
> **Hardware:** X99 Dual-Socket, E5-2680 V4, 64GB RAM, RTX 3090

---

## Role

Proxmox VE hypervisor host. All VMs and LXCs run on this server.

## Specifications

| Component | Detail |
|-----------|--------|
| CPU | Intel Xeon E5-2680 V4 (14C/28T) |
| RAM | 64GB DDR4 ECC |
| GPU | NVIDIA RTX 3090 24GB |
| Boot | 2x 512GB NVMe mirror (rpool) |
| Backup | 2x 256GB NVMe mirror (local-backup) |
| Network | PCIe 4-port NIC + Onboard NIC |

## Storage Pools

| Pool | Drives | Purpose |
|------|--------|---------|
| rpool | 2x 512GB NVMe mirror | Proxmox OS + VM boot disks |
| local-backup | 2x 256GB NVMe mirror | VM snapshots + vzdump staging |

## Hosted VMs & LXCs

18 total (16 running). See [[device_service_index]] for full list.

## Key Procedures

- [[proxmox_startup_script]] — Staged boot with NFS health checks
- [[proxmox_update]] — Safe Proxmox updates via tmux
- [[queen_of_hearts_snapshot_script]] — TrueNAS VM snapshot/rollback

---

## Related Documentation

- [[device_hardware_index]] — Full hardware specs
- [[network_topology]] — Network architecture
