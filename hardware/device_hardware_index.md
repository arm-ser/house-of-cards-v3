# Device Hardware Index

Hardware specifications for the X99 server and physical devices.

---

## Primary Server: ace-of-hearts (.111)

| Component | Specification |
|-----------|--------------|
| Platform | X99 Dual-Socket Motherboard |
| CPU | Intel Xeon E5-2680 V4 (14 cores / 28 threads, 2.4-3.3 GHz) |
| RAM | 64GB DDR4 ECC |
| GPU | NVIDIA RTX 3090 (24GB GDDR6X VRAM) |
| Boot Storage | 2x 512GB M.2 NVMe (ZFS mirror — rpool) |
| Backup Storage | 2x 256GB M.2 NVMe (ZFS mirror — local-backup) |
| Network | PCIe 4-port NIC (passed to OPNsense) + Onboard NIC |
| OS | Proxmox VE |
| Power | ~200W typical |

### Storage Passed to TrueNAS (queen-of-hearts)

| Drives | Config | Pool | Usable | Purpose |
|--------|--------|------|--------|---------|
| 2x 500GB SSD | Mirror | vault | ~500GB | Encrypted sensitive data |
| 2x 1TB SSD | Mirror | gallery | ~1TB | Encrypted photo library |
| 6x 8TB HDD | RAIDZ2 | bulk | ~32TB | Media, backups, downloads |

**Total Raw Storage:** 27.8TB across 13 physical disks (5 NVMe, 5 SSD, 3 HDD)

### VM Resource Allocation

| VM/LXC | vCPU | RAM | Disk |
|--------|------|-----|------|
| king-of-hearts (OPNsense) | 2 | 4GB | 16GB |
| queen-of-hearts (TrueNAS) | 4 | 16GB | 32GB |
| jack-of-hearts (DNS) | 1 | 256MB | 4GB |
| ten-of-hearts (NPMPlus/Portainer) | 1 | 1GB | 4GB |
| five-of-hearts (UrBackup) | 1 | 512MB | 8GB |
| ace-of-diamonds (Secure) | 4 | 6GB | 24GB |
| queen-of-diamonds (Nextcloud) | 4 | 6GB | 24GB |
| king-of-diamonds (Immich) | 4 | 6GB | 24GB |
| ace-of-spades (Media) | 3 | 4GB | 16GB |
| ace-of-clubs (AI/Automation) | 4 | 4GB | 24GB |
| two-of-clubs (UrBackup DMZ) | 2 | 2GB | 16GB |
| three-of-clubs (Home Assistant) | 2 | 2GB | 16GB |
| jack-of-diamonds (Management) | 1 | 512MB | 8GB |
| **Totals** | **33** | **~53GB** | **~216GB** |

**Headroom:** ~11GB RAM free, ~296GB NVMe free

### Future Expansion

When 2nd CPU (E5-2680 V4) + 64GB RAM added:
- ace-of-clubs: boost to 24-32GB (Ollama AI inference)
- queen-of-hearts: boost to 32GB (ZFS ARC cache)
- Remaining: distribute to Nextcloud, Immich, Media

---

## Physical Devices

| Device | Role | Specs | Network | IP | Power |
|--------|------|-------|---------|-----|-------|
| ace-of-hearts | Proxmox host | See above | LAN | .111 | ~200W |
| wall-of-cards | Backup OPNsense | N3700, 8GB RAM | All | .3 | 15W |
| Raspberry Pi | Backup DNS | — | LAN | .4 | 5W |
| ten-of-spades | Gaming PC | i7-9700K, RTX 5070, 32GB | LAN+GUST | .160 | Varies |
| joker-black | Admin laptop | Dell 16 Plus | GUST+VPN | 147.5 | Varies |

**Total always-on power:** ~275W

## Retired Hardware

| Item | Source | Specs | Status |
|------|--------|-------|--------|
| i7-8700K + ASRock H370M-ITX/ac | two-of-diamonds | CPU + motherboard | Retired — working |
| i7-8700K + iBuypower Z370 | three-of-diamonds | CPU + motherboard | Retired — working |
| CTONE Mini PC | six-of-spades | N95, 16GB RAM, 512GB M.2 | Spare |
| GMKtec Mini PC | queen-of-diamonds (old) | N4100, 6GB RAM, 128GB eMMC | Spare |
| MINISFORUM GK41 | (was Home Assistant) | J4125, 8GB RAM, 128GB SSD | Spare |

---

## Related Documentation

- [[naming_index]] — Card naming convention
