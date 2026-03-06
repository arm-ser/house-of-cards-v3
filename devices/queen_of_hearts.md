# queen-of-hearts

> **Type:** VM (ID: 100) | **IP:** 192.168.1.122 | **Network:** LAN
> **Resources:** 4 vCPU, 16GB RAM, 32GB disk

---

## Role

TrueNAS SCALE storage server. Manages all ZFS pools and serves data via NFS/SMB to other VMs.

## Storage Pools

| Pool | Drives | Config | Usable | Encrypted |
|------|--------|--------|--------|-----------|
| vault | 2x 500GB SSD | Mirror | ~500GB | Yes |
| gallery | 2x 1TB SSD | Mirror | ~1TB | Yes |
| media | — | — | — | No |
| bulk | 6x 8TB HDD | RAIDZ2 | ~32TB | No |

## NFS Shares

| Dataset | Mounted By | Purpose |
|---------|-----------|---------|
| vault/services | ace-of-diamonds (.131) | Secure service configs |
| vault/nextcloud | queen-of-diamonds (.142) | Nextcloud files |
| vault/config | jack-of-diamonds (.141) | Config backups |
| vault/docker | five-of-hearts (.115) | UrBackup target |
| gallery/gallery | king-of-diamonds (.143) | Immich photos |
| media/collection | ace-of-spades (.151) | Media files |
| bulk/media/* | ace-of-spades (.151) | Media automation |
| bulk/downloads | ace-of-spades (.151) | Download staging |
| bulk/backup/docker | five-of-hearts (.115) | Docker backup archive |

## Snapshot Schedule

| Dataset | Retention | Frequency |
|---------|-----------|-----------|
| vault/services | 2 months | Daily |
| vault/config | 2 months | Daily |
| vault/nextcloud | 2 weeks | Mon/Wed/Fri 8:08AM |
| gallery/gallery | 2 weeks | Daily |
| media/collection | 3 days | Daily |

## Users & Groups

| Group | GID | Purpose |
|-------|-----|---------|
| truenas_admin | 950 | Full admin |
| smb_users | 3000 | Samba authentication |

---

## Related Documentation

- [[queen_of_hearts_snapshot_script]] — Snapshot/rollback scripts
- [[pass_hard_drives_proxmox_to_truenas]] — Disk passthrough
- [[device_hardware_index]] — Storage specs
