# Quick Reference

Single-page cheat sheet for the entire homelab.

---

## IP Scheme

`192.168.1.{suit_base + rank}` — Hearts=110, Diamonds=130, Spades=150, Clubs=170

Rank: Ace=1, Two=2, ..., Ten=10, Jack=11, Queen=12, King=13

---

## LAN (192.168.1.0/24)

| IP | Name | Type | Role |
|----|------|------|------|
| .1 | CARP VIP | Virtual | Default gateway |
| .3 | wall-of-cards | Physical | Backup OPNsense (CARP secondary) |
| .4 | Raspberry Pi | Physical | Backup DNS (Technitium) |
| .111 | ace-of-hearts | Physical | Proxmox host |
| .115 | five-of-hearts | LXC | UrBackup server |
| .120 | ten-of-hearts | LXC | NPMPlus + Portainer |
| .121 | jack-of-hearts | LXC | Technitium DNS (primary) |
| .122 | queen-of-hearts | VM | TrueNAS SCALE |
| .123 | king-of-hearts | VM | OPNsense firewall |
| .131 | ace-of-diamonds | VM | Secure services (Vaultwarden, Firefly III) |
| .141 | jack-of-diamonds | VM | Encrypted disk (planned) |
| .142 | queen-of-diamonds | VM | Nextcloud + OnlyOffice |
| .143 | king-of-diamonds | VM | Immich |
| .151 | ace-of-spades | VM | Media stack (Jellyfin, *arr) |
| .160 | ten-of-spades | Physical | Gaming PC |
| .171 | ace-of-clubs | VM | AI + Automation (n8n, HedgeDoc) |

## GUST (192.168.147.0/24)

| IP | Device |
|----|--------|
| .1 | CARP VIP (gateway) |
| .5 | joker-black (admin laptop) |
| .10 | ten-of-spades (Gaming PC — GUST NIC) |
| .15 | GUST AP (Netgear AX1800) |

## IoT (192.168.3.0/24) — No Internet

| IP | Device |
|----|--------|
| .1 | CARP VIP (no internet routed) |
| .15 | IoT AP (Netgear R7900P) |
| .173 | three-of-clubs (Home Assistant) |

## DMZ (172.16.10.0/24)

| IP | Device |
|----|--------|
| .1 | CARP VIP |
| .172 | two-of-clubs (UrBackup) |

---

## Storage (TrueNAS — queen-of-hearts .122)

| Pool | Drives | Usable | Purpose |
|------|--------|--------|---------|
| vault | 2x 500GB SSD mirror | ~500GB | Encrypted — Nextcloud, services, configs |
| gallery | 2x 1TB SSD mirror | ~1TB | Encrypted — Immich photos |
| bulk | 6x 8TB HDD RAIDZ2 | ~32TB | Media, backups, downloads |

## Proxmox Storage (ace-of-hearts .111)

| Pool | Drives | Usable | Purpose |
|------|--------|--------|---------|
| rpool | 2x 512GB NVMe mirror | ~512GB | OS + VM boot disks |
| local-backup | 2x 256GB NVMe mirror | ~256GB | Snapshots + vzdump |

---

## Key Services

| Service | VM | Access |
|---------|----|--------|
| Proxmox | ace-of-hearts | .111:8006 |
| TrueNAS | queen-of-hearts | nas.octachainlabs.com |
| Portainer | ten-of-hearts | portainer.octachainlabs.com |
| Vaultwarden | ace-of-diamonds | warden.octachainlabs.com |
| Nextcloud | queen-of-diamonds | (via NPMPlus) |
| Immich | king-of-diamonds | (via NPMPlus) |
| Jellyfin | ace-of-spades | (via NPMPlus) |
| n8n | ace-of-clubs | (via NPMPlus) |
| Home Assistant | three-of-clubs | 192.168.3.173:8123 |

---

## Standards

- **OS:** Debian 13 on all VMs
- **Docker data:** `/home/user/docker/` on all servers
- **Backups:** UrBackup → vault/docker dataset
- **DNS:** Technitium on jack-of-hearts (.121)
- **Reverse Proxy:** NPMPlus on ten-of-hearts (.120) — *.octachainlabs.com
- **Monitoring:** Pulse platform
