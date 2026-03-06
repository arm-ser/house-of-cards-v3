# king-of-hearts

> **Type:** VM (ID: 245) | **IP:** 192.168.1.123 | **Network:** LAN (management)
> **Resources:** 2 vCPU, 4GB RAM, 16GB disk

---

## Role

OPNsense firewall and router. Manages all 5 network segments with CARP failover to wall-of-cards.

## Network Interfaces

| Interface | Network | Physical/Virtual |
|-----------|---------|-----------------|
| PCIe Port 1 | WAN | PCIe passthrough |
| PCIe Port 2 | LAN | PCIe passthrough |
| PCIe Port 3 | GUST | PCIe passthrough |
| PCIe Port 4 | IoT | PCIe passthrough |
| virtio | vmbr0-3 | Virtual bridges |

## Services

| Service | Description |
|---------|-------------|
| Firewall | Stateful packet filtering across 5 segments |
| DHCP | Static mappings + pools for all segments |
| CARP | High-availability failover with wall-of-cards |
| WireGuard VPN | Remote access for mobile devices + admin laptop |
| NAT | Outbound NAT for all segments |

## CARP Configuration

| VIP | Network | Primary | Backup |
|-----|---------|---------|--------|
| .1 | LAN | king-of-hearts (.123) | wall-of-cards (.3) |
| .1 | GUST | king-of-hearts | wall-of-cards |
| .1 | IoT | king-of-hearts | wall-of-cards |
| .1 | DMZ | king-of-hearts | wall-of-cards |

---

## Related Documentation

- [[firewall_rules]] — OPNsense firewall rules
- [[network_topology]] — Network segments
- [[dhcp_configuration]] — DHCP settings
