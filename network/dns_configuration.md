# DNS Configuration

Primary DNS: Technitium DNS Server on jack-of-hearts (LXC, .121)
Backup DNS: Technitium on Raspberry Pi (.4)

---

## Technitium DNS Features

- Recursive DNS resolution
- Ad blocking via blocklists
- Split-horizon DNS for internal/external resolution
- DNS-over-HTTPS/TLS support
- DNSSEC validation

## DNS Servers

| Server | IP | Type | Role |
|--------|-----|------|------|
| jack-of-hearts | 192.168.1.121 | LXC | Primary DNS |
| Raspberry Pi | 192.168.1.4 | Physical | Backup DNS |

DHCP pushes both IPs — clients automatically failover if primary is down.

## Local DNS Entries

> **TODO**: Export current entries from Technitium. Below are the expected entries.

All services accessible via `*.octachainlabs.com` through NPMPlus reverse proxy with Certbot SSL.

### Proxy Hosts (NPMPlus on ten-of-hearts .120)

| Subdomain | Target | Service |
|-----------|--------|---------|
| analytics.octachainlabs.com | — | Analytics |
| audiobooks.octachainlabs.com | — | Audiobooks |
| backup.octachainlabs.com | — | UrBackup |
| ccdb.octachainlabs.com | — | CommaFeed |
| nas.octachainlabs.com | — | TrueNAS |
| nbn.octachainlabs.com | — | NocoDB |
| pdash.octachainlabs.com | — | Dashboard |
| portainer.octachainlabs.com | — | Portainer |
| qbit.octachainlabs.com | — | qBittorrent |
| radarr.octachainlabs.com | — | Radarr |
| request.octachainlabs.com | — | Jellyseerr |
| rss.octachainlabs.com | — | RSS |
| search.octachainlabs.com | — | SearXNG |
| semaphore.octachainlabs.com | — | Semaphore |
| share.octachainlabs.com | — | File sharing |
| sonarr.octachainlabs.com | — | Sonarr |
| update.octachainlabs.com | — | Tugtainer |
| warden.octachainlabs.com | — | Vaultwarden |
| yfdl.octachainlabs.com | — | YFDL |
| ytwatcher.octachainlabs.com | — | YouTube watcher |

---

## Related Documentation

- [[network_topology]] — Network overview
- [[dhcp_configuration]] — IP assignments
