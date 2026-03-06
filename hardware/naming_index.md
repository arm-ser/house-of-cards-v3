# Naming Convention

All network devices follow a playing card deck naming scheme with semantic meaning.

---

## Suit Domains

| Suit | Domain | IP Base (LAN) | Meaning |
|------|--------|---------------|---------|
| Hearts | Core Infrastructure | 192.168.1.110+ | Firewall, DNS, storage, hypervisor, proxy |
| Diamonds | Productivity & Data | 192.168.1.130+ | Sensitive data: Nextcloud, Vaultwarden, Firefly, Immich |
| Spades | Media & Downloads | 192.168.1.150+ | Jellyfin, *arr stack, VPN tunnels |
| Clubs | Automation & AI | 192.168.1.170+ | n8n, Home Assistant, AI tools |

## IP Encoding

IPs encode the card identity: `192.168.1.{suit_base + rank}`

**Rank mapping:** Ace=1, Two=2, Three=3, ..., Ten=10, Jack=11, Queen=12, King=13

| Example | Calculation | IP |
|---------|------------|-----|
| ace-of-hearts | 110 + 1 | .111 |
| king-of-hearts | 110 + 13 | .123 |
| ace-of-diamonds | 130 + 1 | .131 |
| queen-of-diamonds | 130 + 12 | .142 |
| ace-of-spades | 150 + 1 | .151 |
| ace-of-clubs | 170 + 1 | .171 |

**Cross-subnet encoding** uses the same rank:
- two-of-clubs on DMZ: 172.16.10.172
- three-of-clubs on IoT: 192.168.3.173

## Special Names

| Name | Role | Why Special |
|------|------|-------------|
| wall-of-cards | Backup OPNsense (.3) | Physical appliance, no card rank |
| joker-black | Admin laptop | Joker = admin/wildcard |
| joker-red | (Reserved) | Future personal device |

## Card Name Reuse

Some names were reused between old physical devices and new VMs:

| Card Name | Old (Decommissioned) | New (Current) |
|-----------|---------------------|---------------|
| queen-of-diamonds | GMKtec N4100 mini PC | Proxmox VM — Nextcloud + OnlyOffice |
| king-of-diamonds | TrueNAS VM on two-of-diamonds | Proxmox VM — Immich |
| jack-of-diamonds | MeLE N5105 mini PC | Proxmox VM — Encrypted disk (planned) |

## Current Card Assignments

| Card | Type | IP | Role |
|------|------|----|------|
| ace-of-hearts | Physical | .111 | Proxmox host |
| five-of-hearts | LXC | .115 | UrBackup server |
| ten-of-hearts | LXC | .120 | NPMPlus + Portainer |
| jack-of-hearts | LXC | .121 | Technitium DNS |
| queen-of-hearts | VM | .122 | TrueNAS SCALE |
| king-of-hearts | VM | .123 | OPNsense firewall |
| ace-of-diamonds | VM | .131 | Secure services |
| jack-of-diamonds | VM | .141 | Encrypted disk (planned) |
| queen-of-diamonds | VM | .142 | Nextcloud |
| king-of-diamonds | VM | .143 | Immich |
| ace-of-spades | VM | .151 | Media stack |
| ten-of-spades | Physical | .160 | Gaming PC |
| ace-of-clubs | VM | .171 | AI + Automation |
| two-of-clubs | VM | 172.16.10.172 | UrBackup (DMZ) |
| three-of-clubs | VM | 192.168.3.173 | Home Assistant |

---

## Related Documentation

- [[device_hardware_index]] — Hardware specifications
