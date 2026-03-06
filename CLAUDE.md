# CLAUDE.md

AI agent guidance for this project.

---

## Quick Start for AI Agents

**New to this homelab?** Start here:
1. Read [[README|README.md]] (root) — Complete overview, device table, network segments
2. Check [[documentation/quick_reference|quick_reference.md]] — Single-page cheat sheet with all IPs and services
3. For specific tasks:
   - Device info → [[devices/device_service_index|device_service_index.md]]
   - Network layout → [[network/network_topology|network_topology.md]]
   - Hardware specs → [[hardware/device_hardware_index|device_hardware_index.md]]

**Project status**: Post-migration, fully operational (2026-03). Single X99 server running 18 VMs/LXCs across 5 network segments.

---

## Token-Efficient Edits

**Rule:** Replace ONLY the changing substring, never full lines/rows/paragraphs.

---

## Folders to Ignore

- Ignore attachments folder and do not look inside.

---

## Communication Rules

**Default Behavior**:
- Concise and direct — answer only what's asked
- Ask clarifying questions FIRST before solutions
- Stop after answering — no "next steps" or extras
- Assume user expertise — skip basics
- Default <300 words unless specified

**Professional Objectivity**:
Prioritize accuracy over validation. Challenge weak reasoning, identify flaws, present alternatives.

**Response Format**:
- Bullets/lists, 1-2 examples max, ask if more detail needed
- No unprompted guides, bonus tips, filler phrases, or educational context

**Override**: Prefix with "FULL DETAIL MODE" for comprehensive responses

---

## Formatting Guidelines

**File naming:** snake_case (e.g., `my_note_name.md`)

**Full standards:** See `documentation/formatting_standards.md` for headers, wikilinks, lists, callouts, and tagging conventions.

---

## Project Context

Obsidian vault and GitHub portfolio for homelab infrastructure documentation. This is v3 — the post-migration documentation of a consolidated single-server homelab.

**Key Infrastructure:**
- **Server:** X99 dual-socket, E5-2680 V4, 64GB RAM, RTX 3090
- **Hypervisor:** Proxmox VE (ace-of-hearts, .111)
- **Storage:** TrueNAS SCALE (queen-of-hearts, .122) — 27.8TB across 13 disks
- **Firewall:** OPNsense VM (king-of-hearts, .123) with CARP failover
- **DNS:** Technitium (jack-of-hearts, .121) + Raspberry Pi backup (.4)
- **Reverse Proxy:** NPMPlus (ten-of-hearts, .120)
- **Backups:** UrBackup (five-of-hearts, .115)
- **Monitoring:** Pulse platform
- **OS Standard:** Debian 13 on all VMs

---

## Vault Structure

```
├── devices/                Per-VM/LXC documentation
├── network/                Network topology and configuration
├── hardware/               Hardware index and naming convention
├── infrastructure/         Proxmox, TrueNAS, Linux procedures
│   ├── proxmox/
│   ├── truenas/
│   └── linux/
│       ├── security/
│       ├── general/
│       └── backup/
├── documentation/          Quick reference, formatting standards
├── fixes/                  Fix procedures
└── attachments/            Device images
```

**Key Files:**
- `documentation/quick_reference.md` — IP/device cheat sheet
- `hardware/device_hardware_index.md` — Hardware specs + VM allocation
- `hardware/naming_index.md` — Card naming scheme + IP encoding
- `network/network_topology.md` — Network layout, bridges, traffic flow
- `network/service_matrix.md` — Service-to-VM mapping
- `devices/device_service_index.md` — Master device index
- `devices/[card_name].md` — Per-device documentation

---

## Device Naming

Network devices follow a playing card deck naming scheme. See `hardware/naming_index.md` for the convention and IP encoding.

**Suit domains:** Hearts = Infrastructure, Diamonds = Productivity, Spades = Media, Clubs = Automation

---

## Docker Standards

**Data Directory Convention:**
- All Docker container data is stored in `/home/user/docker/` on all servers
- Each service has its own subdirectory: `/home/user/docker/[service-name]`
- This standardized path simplifies backups and maintenance across the homelab
