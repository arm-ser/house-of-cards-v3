# Service Matrix

Complete mapping of services to VMs/LXCs.

---

## ace-of-diamonds (.131) — Secure Services

| Service | Description |
|---------|-------------|
| Vaultwarden | Password manager |
| Firefly III | Personal finance + MariaDB + cron |
| ProtonMail Bridge | Email bridge (pending redeploy) |
| Portainer Agent | Connected to main Portainer on ten-of-hearts |

## queen-of-diamonds (.142) — Nextcloud

| Service | Description |
|---------|-------------|
| Nextcloud | File sync and sharing + MariaDB |
| OnlyOffice | Document editing (pending connection) |
| Portainer CE | Standalone container management |

## king-of-diamonds (.143) — Immich

| Service | Description |
|---------|-------------|
| Immich Server | Photo/video management |
| Immich ML | Machine learning (face detection, search) |
| PostgreSQL + pgvector | Immich database |
| Redis | Immich cache |
| Portainer CE | Standalone container management |

## ace-of-spades (.151) — Media Stack

| Service | Description |
|---------|-------------|
| Gluetun | ProtonVPN tunnel — routes download traffic |
| qBittorrent | Torrent client (via Gluetun) |
| Sonarr | TV show automation (via Gluetun) |
| Radarr | Movie automation (via Gluetun) |
| Jackett | Torrent indexer proxy (via Gluetun) |
| FlareSolverr | Cloudflare bypass for Jackett |
| Jellyfin | Media library |
| Jellyfin (YouTube) | YouTube library |
| Jellyseerr | Media request management |
| Metube | YouTube downloader (via Gluetun) |
| Pinchflat | YouTube archiver (via Gluetun) |
| SearXNG + Redis | Meta search engine |
| Watchtower | Auto-update containers |
| Portainer Agent | Connected to main Portainer |

## ace-of-clubs (.171) — AI + Automation

| Service | Description |
|---------|-------------|
| n8n | Workflow automation + PostgreSQL + Redis |
| NocoDB | Database UI |
| HedgeDoc | Collaborative markdown editor + PostgreSQL |
| Homarr | Dashboard |
| CommaFeed | RSS reader |
| Cloudflared | Cloudflare tunnel |
| Portainer Agent | Connected to main Portainer |

**Planned (requires GPU passthrough):**
| Service | Description |
|---------|-------------|
| Ollama | LLM inference (RTX 3090) |
| OpenWebUI | Chat interface for Ollama |

## two-of-clubs (172.16.10.172) — DMZ

| Service | Description |
|---------|-------------|
| UrBackup | Isolated backup instance |

## three-of-clubs (192.168.3.173) — IoT

| Service | Description |
|---------|-------------|
| Home Assistant OS | Smart home automation |
| Matter Server | Smart home protocol |
| Homebridge | HomeKit bridge |

## Infrastructure LXCs

### jack-of-hearts (.121) — DNS
| Service | Description |
|---------|-------------|
| Technitium DNS | Primary DNS, ad blocking, split-horizon |

### ten-of-hearts (.120) — Reverse Proxy & Management
| Service | Description |
|---------|-------------|
| NPMPlus | Reverse proxy with Certbot SSL |
| Portainer | Main server — 3-agent license |

### five-of-hearts (.115) — Backup
| Service | Description |
|---------|-------------|
| UrBackup Server | Centralized backup for all Docker VMs |

---

## Portainer License Allocation

| Instance | Location | Type |
|----------|----------|------|
| Main Server | ten-of-hearts (.120) | Portainer Server |
| Agent 1 | ace-of-diamonds (.131) | Agent |
| Agent 2 | ace-of-spades (.151) | Agent |
| Agent 3 | ace-of-clubs (.171) | Agent |
| Standalone CE | queen-of-diamonds (.142) | Portainer CE |
| Standalone CE | king-of-diamonds (.143) | Portainer CE |

---

## Related Documentation

- [network_topology](https://github.com/arm-ser/house-of-cards-v3/edit/master/network/network_topology.md) — Network segments and bridges
- [dhcp_configuration](https://github.com/arm-ser/house-of-cards-v3/blob/master/network/dhcp_configuration.md) — Static IP mappings

