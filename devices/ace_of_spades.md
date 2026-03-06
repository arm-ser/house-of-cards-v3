# ace-of-spades

> **Type:** VM (ID: 151) | **IP:** 192.168.1.151 | **Network:** LAN
> **Resources:** 3 vCPU, 4GB RAM, 16GB disk
> **OS:** Debian 13

---

## Role

Media stack VM. All download traffic routed through ProtonVPN via Gluetun. Jellyfin serves media library.

## Services

| Service | Description |
|---------|-------------|
| Gluetun | ProtonVPN tunnel — starts first, routes download traffic |
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

## Storage Mounts

| Mount Point | Source | Type |
|-------------|--------|------|
| media/collection | queen-of-hearts (.122) | NFS |
| bulk/media/* | queen-of-hearts (.122) | NFS |
| bulk/downloads | queen-of-hearts (.122) | NFS |

## Docker Data

`/home/user/docker/` — backed up by UrBackup on five-of-hearts

---

## Related Documentation

- [[queen_of_hearts]] — Media and bulk pools
- [[protonvpn_update_watcher]] — Auto-restart VPN-dependent containers
- [[service_matrix]] — Full service listing
