# Firewall Rules

OPNsense firewall rules managed by king-of-hearts VM (.123).

> **TODO**: Export actual OPNsense rules from live system. Below is the intended rule framework.

---

## Inter-VLAN Rules

### LAN (192.168.1.0/24)
- Allow all outbound traffic
- Allow access to all other segments

### GUST (192.168.147.0/24)
- Allow internet access
- Allow DNS queries to jack-of-hearts (.121)
- Allow limited LAN access (NAS file shares, specific services)
- Block access to DMZ
- Block access to IoT
- Block access to Proxmox management (.111:8006)

### IoT (192.168.3.0/24)
- **Block all internet access**
- Allow local communication between IoT devices
- Allow Home Assistant (192.168.3.173) full local access
- Block access to LAN, DMZ, GUST

### DMZ (172.16.10.0/24)
- Allow outbound via Cloudflare tunnel only
- Allow DNS queries to jack-of-hearts (.121) — cross-VLAN rule
- Allow NFS access to TrueNAS (.122) — cross-VLAN rule for vault/docker
- Block all other inbound except established connections
- Block access to LAN, GUST, IoT

## Cross-VLAN NFS Rules

| Source | Destination | Ports | Purpose |
|--------|------------|-------|---------|
| two-of-clubs (172.16.10.172) | queen-of-hearts (.122) | NFS (2049, 111) | Backup data access |

## VPN (WireGuard)

- Full access to LAN, GUST, DMZ, Internet
- Managed inside OPNsense
- Peers: mobile phones, admin laptop (joker-black)

---

## Related Documentation

- [network_topology](https://github.com/arm-ser/house-of-cards-v3/edit/master/network/network_topology.md) — Network segments and bridges
- [dhcp_configuration](https://github.com/arm-ser/house-of-cards-v3/blob/master/network/dhcp_configuration.md) — Static IP mappings
