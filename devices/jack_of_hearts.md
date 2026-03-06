# jack-of-hearts

> **Type:** LXC | **IP:** 192.168.1.121 | **Network:** LAN
> **Resources:** 1 vCPU, 256MB RAM, 4GB disk

---

## Role

Primary DNS server running Technitium DNS. Provides recursive resolution, ad blocking, and split-horizon DNS for all network segments.

## Services

| Service | Description |
|---------|-------------|
| Technitium DNS | Full-featured DNS server |

## DNS Features

- Recursive DNS resolution
- Ad blocking via blocklists
- Split-horizon DNS (internal/external)
- DNS-over-HTTPS/TLS
- DNSSEC validation

## Redundancy

Backup DNS on Raspberry Pi (.4) also running Technitium. DHCP pushes both IPs for automatic failover.

---

## Related Documentation

- [[dns_configuration]] — DNS entries and setup
- [[dhcp_configuration]] — DHCP pushes DNS IPs
