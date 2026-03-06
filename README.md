# House of Cards v3

From 7 mini PCs scattered across a desk to a single dual-socket X99 server running 18 VMs and LXCs — this is the third iteration of my homelab, rebuilt from the ground up during a full infrastructure migration.

> **AI-Powered Documentation**: This homelab documentation is maintained with assistance from [Claude Code](https://claude.com/claude-code).

> **Note**: All IP addresses, device names, and sensitive identifiers in this documentation have been randomized and anonymized for privacy and security purposes.

**Skills Demonstrated**:
- Single-server virtualization (Proxmox VE — 18 VMs/LXCs on one host)
- Network segmentation (5 isolated networks via OPNsense VM with CARP failover)
- ZFS storage architecture (TrueNAS SCALE — 27.8TB, encrypted datasets, automated snapshots)
- Docker containerization & orchestration (40+ services across 6 Docker VMs)
- Infrastructure automation (Ansible/Semaphore, staged boot scripts, NFS health checks)
- Security hardening (SSH-only access, encrypted storage, DMZ isolation, WireGuard VPN)

**Quick Stats**: 1 server | 18 VMs/LXCs | 5 networks | 40+ services | 27.8TB storage
> See [documentation/quick_reference.md](documentation/quick_reference.md) for full inventory

---

## Previous Versions

This homelab has evolved significantly over time:

| Version | Repository | Description |
|---------|-----------|-------------|
| **v3** (current) | You are here | Single X99 server, 18 VMs/LXCs, OPNsense, Technitium DNS |
| **v2** | [house-of-cards-v1](https://github.com/arm-ser/house-of-cards-v1) | 7 physical devices, pfSense, Pi-hole, 40+ services |
| **v1** | [house-of-cards](https://github.com/arm-ser/house-of-cards) | Original homelab setup |

---

## Network Architecture

The homelab uses **5 isolated network segments** managed by an OPNsense VM (king-of-hearts) with CARP failover to a physical backup appliance (wall-of-cards).

**Network Documentation**: [network_topology.md](network/network_topology.md) | [firewall_rules.md](network/firewall_rules.md) | [dhcp_configuration.md](network/dhcp_configuration.md) | [dns_configuration.md](network/dns_configuration.md)

```
                                    INTERNET
                                        |
                                   Arris TM1602A
                                    ISP Modem
                                        |
                                   PCIe Port 1 (WAN)
                                        |
                          ┌─────────────────────────────┐
                          │  king-of-hearts (OPNsense)  │
                          │  VM 245 — PCIe 4-port NIC   │
                          │  CARP Primary (.123)        │
                          └─────────────────────────────┘
                                        |
        ┌───────────────────────────────┼────────────────┬──────────────────┐
        │                               │                │                  │
        │ PCIe Port 2                   │ PCIe Port 3    │ PCIe Port 4      │
        │ LAN                           │ GUST           │ IoT              │
        │                               │                │                  │
  ┌─────┴──────┐                 ┌──────┴──────┐   ┌─────┴──────┐           │
  │  8-port    │                 │ Netgear     │   │ Netgear    │           │
  │  Switch    │                 │ AX1800      │   │ R7900P     │           │
  │ TL-SG108E  │                 │ (WiFi 6)    │   │ (AP Mode)  │           │
  └─────┬──────┘                 └─────────────┘   └────────────┘           │
        │                                                                   │
        │ Onboard NIC                                                       │
        │                                                                   │
  ┌─────┴──────────────────────────────────────────────────────────────┐    │
  │                    ace-of-hearts (.111)                            │    │
  │                    Proxmox VE Host — X99 Server                    │    │
  │                                                                    │    │
  │  ┌─── vmbr0 (LAN 192.168.1.0/24) ─────────────────────────────┐    │    │
  │  │ queen-of-hearts (.122)  TrueNAS SCALE — 27.8TB Storage     │    │    │
  │  │ jack-of-hearts  (.121)  Technitium DNS (Primary)           │    │    │
  │  │ ten-of-hearts   (.120)  NPMPlus + Portainer                │    │    │
  │  │ five-of-hearts  (.115)  UrBackup Server                    │    │    │
  │  │ ace-of-diamonds (.131)  Secure Services (Vaultwarden, etc) │    │    │
  │  │ queen-of-diamonds(.142) Nextcloud + OnlyOffice             │    │    │
  │  │ king-of-diamonds(.143)  Immich (Photos)                    │    │    │
  │  │ ace-of-spades   (.151)  Media Stack (Jellyfin, *arr)       │    │    │
  │  │ ace-of-clubs    (.171)  AI + Automation (n8n, HedgeDoc)    │    │    │
  │  │ jack-of-diamonds(.141)  Encrypted Disk (Planned)           │    │    │
  │  └────────────────────────────────────────────────────────────┘    │    │
  │                                                                    │    │
  │  ┌─── vmbr1 (DMZ 172.16.10.0/24) ─────────────────────────────┐    │    │
  │  │ two-of-clubs (172.16.10.172)  UrBackup (Offsite Backup)    │    │    │
  │  └────────────────────────────────────────────────────────────┘    │    │
  │                                                                    │    │
  │  ┌─── vmbr2 (IoT 192.168.3.0/24) ──────────────────────────────┐   │    │
  │  │ three-of-clubs (192.168.3.173)  Home Assistant              │───┘    │
  │  └─────────────────────────────────────────────────────────────┘        │
  │                                                                         │
  │  ┌─── vmbr3 (GUST 192.168.147.0/24) ───────────────────────────┐        │
  │  │ (Future guest-facing VMs)                                   │        │
  │  └─────────────────────────────────────────────────────────────┘        │
  └─────────────────────────────────────────────────────────────────────────┘

╔══════════════════════════════════════════════════════════════════════════╗
║                          NETWORK ISOLATION RULES                         ║
╠══════════════════════════════════════════════════════════════════════════╣
║  VPN   →  Full access to LAN, GUST, DMZ, Internet                        ║
║  LAN   →  Full access to all segments                                    ║
║  GUST  →  Limited LAN access (DNS, NAS), blocked from DMZ                ║
║  IoT   →  No internet — local only, Home Assistant communicates          ║
║  DMZ   →  Strict isolation, outbound via Cloudflare tunnel only          ║
╚══════════════════════════════════════════════════════════════════════════╝
```

**Network Highlights**:
- **CARP failover**: OPNsense VM (king-of-hearts) + physical backup (wall-of-cards) — automatic sub-second failover via VIP .1
- **5 isolated segments**: LAN, DMZ, IoT, GUST, WAN — each with dedicated Proxmox bridge
- **PCIe passthrough**: 4-port NIC passed directly to OPNsense VM for wire-speed routing
- **Backup DNS**: Raspberry Pi (.4) running Technitium secondary — automatic client failover

---

## Server Architecture

Everything runs on a single X99 dual-socket server.

**Hardware Specs**: [device_hardware_index.md](hardware/device_hardware_index.md) | **Naming Convention**: [naming_index.md](hardware/naming_index.md)

### Host: ace-of-hearts (.111)

| Component | Specification |
|-----------|--------------|
| Platform | X99 Dual-Socket |
| CPU | Intel Xeon E5-2680 V4 (14C/28T) |
| RAM | 64GB DDR4 ECC |
| GPU | NVIDIA RTX 3090 (24GB VRAM) |
| Boot Storage | 2x 512GB NVMe (ZFS mirror — rpool) |
| Backup Storage | 2x 256GB NVMe (ZFS mirror — local-backup) |
| NIC | PCIe 4-port (passed to OPNsense) + Onboard |
| OS | Proxmox VE |

### VM & LXC Resource Allocation

| Name | Type | ID | vCPU | RAM | Disk | Bridge | IP | Role |
|------|------|----|------|-----|------|--------|-----|------|
| king-of-hearts | VM | 245 | 2 | 4GB | 16GB | PCIe passthrough | .123 | OPNsense Firewall |
| queen-of-hearts | VM | 100 | 4 | 16GB | 32GB | vmbr0 | .122 | TrueNAS SCALE |
| jack-of-hearts | LXC | — | 1 | 256MB | 4GB | vmbr0 | .121 | Technitium DNS |
| ten-of-hearts | LXC | — | 1 | 1GB | 4GB | vmbr0 | .120 | NPMPlus + Portainer |
| five-of-hearts | LXC | — | 1 | 512MB | 8GB | vmbr0 | .115 | UrBackup Server |
| ace-of-diamonds | VM | — | 4 | 6GB | 24GB | vmbr0 | .131 | Secure Services |
| queen-of-diamonds | VM | 142 | 4 | 6GB | 24GB | vmbr0 | .142 | Nextcloud + OnlyOffice |
| king-of-diamonds | VM | 143 | 4 | 6GB | 24GB | vmbr0 | .143 | Immich (Photos) |
| ace-of-spades | VM | 151 | 3 | 4GB | 16GB | vmbr0 | .151 | Media Stack |
| ace-of-clubs | VM | 171 | 4 | 4GB | 24GB | vmbr0 | .171 | AI + Automation |
| two-of-clubs | VM | 172 | 2 | 2GB | 16GB | vmbr1 | 172.16.10.172 | UrBackup (DMZ) |
| three-of-clubs | VM | 173 | 2 | 2GB | 16GB | vmbr2 | 192.168.3.173 | Home Assistant |
| jack-of-diamonds | VM | — | 1 | 512MB | 8GB | vmbr0 | .141 | Encrypted Disk (Planned) |

**Staged Boot**: VMs start in dependency order — TrueNAS first, wait for NFS health, then NAS-dependent VMs. See [proxmox_startup_script.md](infrastructure/proxmox/proxmox_startup_script.md).

---

## Devices

**Service Mapping**: [device_service_index.md](devices/device_service_index.md) | **Service Matrix**: [service_matrix.md](network/service_matrix.md)

| Device | Role | Description |
|--------|------|-------------|
| [ace-of-hearts](devices/ace_of_hearts.md) | Proxmox Host | X99 server — all VMs/LXCs run here |
| [king-of-hearts](devices/king_of_hearts.md) | OPNsense Firewall | 5-network routing, CARP failover, WireGuard VPN |
| [queen-of-hearts](devices/queen_of_hearts.md) | TrueNAS SCALE | 27.8TB ZFS storage, NFS/SMB, encrypted datasets |
| [jack-of-hearts](devices/jack_of_hearts.md) | Technitium DNS | Primary DNS, ad blocking, split-horizon |
| [ten-of-hearts](devices/ten_of_hearts.md) | NPMPlus + Portainer | Reverse proxy, container management |
| [five-of-hearts](devices/five_of_hearts.md) | UrBackup Server | Centralized backup for all Docker VMs |
| [ace-of-diamonds](devices/ace_of_diamonds.md) | Secure Services | Vaultwarden, Firefly III, ProtonMail Bridge |
| [queen-of-diamonds](devices/queen_of_diamonds.md) | Nextcloud | File sync + OnlyOffice document editing |
| [king-of-diamonds](devices/king_of_diamonds.md) | Immich | Photo/video management with ML |
| [ace-of-spades](devices/ace_of_spades.md) | Media Stack | Jellyfin, Sonarr, Radarr, qBittorrent (via VPN) |
| [ace-of-clubs](devices/ace_of_clubs.md) | AI + Automation | n8n, NocoDB, HedgeDoc, Homarr, CommaFeed |
| [two-of-clubs](devices/two_of_clubs.md) | UrBackup (DMZ) | Isolated backup instance |
| [three-of-clubs](devices/three_of_clubs.md) | Home Assistant | Smart home automation on IoT network |
| [jack-of-diamonds](devices/jack_of_diamonds.md) | Encrypted Disk | Planned: Ansible/Semaphore management VM |
| [ten-of-spades](devices/ten_of_spades.md) | Gaming PC | Dual-homed workstation (LAN + GUST) |
| [joker-black](devices/joker_black.md) | Admin Laptop | Primary admin workstation |

---

## Storage Architecture

All storage is managed by TrueNAS SCALE (queen-of-hearts) with ZFS. VMs mount datasets via NFS.

### Storage Pools

| Pool | Drives | Config | Usable | Purpose |
|------|--------|--------|--------|---------|
| `rpool` | 2x 512GB NVMe | Mirror | ~512GB | Proxmox OS + VM boot disks |
| `local-backup` | 2x 256GB NVMe | Mirror | ~256GB | VM snapshots + vzdump staging |
| `vault` | 2x 500GB SSD | Mirror | ~500GB | Encrypted — Nextcloud, Vaultwarden, Firefly, configs |
| `gallery` | 2x 1TB SSD | Mirror | ~1TB | Encrypted — Immich photo library |
| `media` | — | — | — | Media collection |
| `bulk` | 6x 8TB HDD | RAIDZ2 | ~32TB | Media automation, downloads, backups |

### Key Datasets & NFS Shares

| Dataset | Content | Encrypted | Mounted By |
|---------|---------|-----------|------------|
| vault/services | Docker configs for secure services | Yes | ace-of-diamonds (.131) |
| vault/nextcloud | Nextcloud user files | Yes | queen-of-diamonds (.142) |
| vault/config | Config exports (OPNsense, TrueNAS, Proxmox) | Yes | jack-of-diamonds (.141) |
| vault/docker | UrBackup target for all Docker servers | Yes | five-of-hearts (.115) |
| gallery/gallery | Immich photo/video library | Yes | king-of-diamonds (.143) |
| media/collection | Media files (movies, TV, music) | No | ace-of-spades (.151) |
| bulk/media/* | Media automation staging | No | ace-of-spades (.151) |
| bulk/downloads | Download staging | No | ace-of-spades (.151) |
| bulk/backup/docker | Docker backup archive | No | five-of-hearts (.115) |

### Snapshot Schedule

| Dataset | Retention | Frequency |
|---------|-----------|-----------|
| vault/services | 2 months | Daily |
| vault/config | 2 months | Daily |
| vault/nextcloud | 2 weeks | Mon/Wed/Fri 8:08AM |
| gallery/gallery | 2 weeks | Daily |
| media/collection | 3 days | Daily |

---

## Backup Strategy

| Tier | What | Tool | Frequency | Target |
|------|------|------|-----------|--------|
| **ZFS Snapshots** | TrueNAS datasets | ZFS | Per schedule above | Local to TrueNAS |
| **VM Backups** | All VM/LXC disks | Proxmox vzdump | Weekly | local-backup NVMe → TrueNAS |
| **Docker Backups** | `/home/user/docker/` on all VMs | UrBackup | Continuous | vault/docker dataset |
| **Config Exports** | OPNsense, TrueNAS, Proxmox configs | Cron (planned) | Weekly | vault/config dataset |

---

## Future Roadmap

> **Current State**: Migration complete. Single-server infrastructure fully operational. Security hardening and automation improvements in progress.

### Security Hardening
- SSH-only access across all VMs and LXCs
- Disable root login — SSH key authentication only
- Audit sudo/root configurations per server

### Infrastructure Automation
- Configure RTX 3090 GPU passthrough to ace-of-clubs
- Improve proxmox startup script with service health checks
- Set up weekly config export cron jobs

### DNS & Networking
- Setup Raspberry Pi as backup DNS
- Add local short domains to DNS (e.g., ace.dmnd)

### Storage & Encryption
- Create TrueNAS encryption passphrase + USB key storage
- Create jack-of-diamonds VM for encrypted disk management

### Verification
- Test restore from UrBackup
- 48-hour burn-in: monitor temps, RAM, disk health

---

## Resources and Tools

| Tool | Description | Tutorials |
|------|-------------|-----------|
| [Proxmox VE](https://www.proxmox.com/) | Type-1 hypervisor for all VMs/LXCs | |
| [TrueNAS SCALE](https://www.truenas.com/truenas-scale/) | ZFS-based NAS with Docker support | |
| [OPNsense](https://opnsense.org/) | Open source firewall and router | |
| [Technitium DNS](https://technitium.com/dns/) | Self-hosted DNS with ad blocking | |
| [Tabby](https://github.com/Eugeny/tabby) | Highly configurable terminal emulator | |
| [Ventoy](https://www.ventoy.net/en/index.html) | Easy multiboot USB creator | |
| [Obsidian](https://obsidian.md/) | Note-taking and documentation | |
| [Claude Code](https://claude.com/claude-code) | AI-powered development assistant | |

---

## Teachers

The following creators have been instrumental in my learning journey. Their content simplified complex topics and made them accessible. I encourage you to subscribe and support their work.

[Awesome Open Source](https://www.youtube.com/@AwesomeOpenSource/featured) | [Lawrence Systems](https://www.youtube.com/@LAWRENCESYSTEMS) | [DB Tech](https://www.youtube.com/@DBTechYT) | [Raid Owl](https://www.youtube.com/@RaidOwl) | [Ben Eater](https://www.youtube.com/@BenEater) | [IBRACORP](https://www.youtube.com/@IBRACORP) | [Techno Tim](https://www.youtube.com/@TechnoTim) | [Mark Furneaux](https://www.youtube.com/@TheUbuntuGuy) | [Christian Lempa](https://www.youtube.com/@christianlempa) | [John Hammond](https://www.youtube.com/@_JohnHammond)

---

## Disclaimer

This home lab is intended strictly for educational and experimental purposes. Any use of information provided in this lab for illegal or unethical activities is strictly prohibited. The creator cannot be held responsible for any consequences resulting from misuse. By accessing and using information provided in this lab, you acknowledge and agree to comply with all applicable laws and ethical guidelines. Use this information responsibly and at your own risk.
