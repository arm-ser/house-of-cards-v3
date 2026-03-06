# DHCP Configuration

Static IP assignments managed by OPNsense (king-of-hearts).

---

## LAN (192.168.1.0/24)

Gateway: 192.168.1.1 (CARP VIP)
DNS: 192.168.1.121 (jack-of-hearts) + 192.168.1.4 (Raspberry Pi backup)

| IP | Name | Type | Role |
|----|------|------|------|
| .1 | CARP VIP | Virtual | Default gateway |
| .3 | wall-of-cards | Physical | Backup OPNsense (CARP secondary) |
| .4 | Raspberry Pi | Physical | Backup DNS (Technitium) |
| .111 | ace-of-hearts | Physical | Proxmox host |
| .115 | five-of-hearts | LXC | UrBackup server |
| .120 | ten-of-hearts | LXC | NPMPlus + Portainer |
| .121 | jack-of-hearts | LXC | Technitium DNS (primary) |
| .122 | queen-of-hearts | VM | TrueNAS SCALE |
| .123 | king-of-hearts | VM | OPNsense (management IP) |
| .131 | ace-of-diamonds | VM | Secure services |
| .141 | jack-of-diamonds | VM | Encrypted disk (planned) |
| .142 | queen-of-diamonds | VM | Nextcloud + OnlyOffice |
| .143 | king-of-diamonds | VM | Immich |
| .151 | ace-of-spades | VM | Media stack |
| .160 | ten-of-spades | Physical | Gaming PC (LAN NIC) |
| .171 | ace-of-clubs | VM | AI + Automation |

## GUST (192.168.147.0/24)

Gateway: 192.168.147.1 (CARP VIP)

| IP | Device |
|----|--------|
| .1 | CARP VIP (gateway) |
| .5 | joker-black (admin laptop) |
| .10 | ten-of-spades (Gaming PC — GUST NIC) |
| .15 | GUST AP (Netgear AX1800) |
| .100+ | DHCP pool |

## IoT (192.168.3.0/24)

Gateway: 192.168.3.1 (CARP VIP) — **No internet routed**

| IP | Device |
|----|--------|
| .1 | CARP VIP (gateway — no internet) |
| .15 | IoT AP (Netgear R7900P) |
| .173 | three-of-clubs (Home Assistant) |
| .100+ | DHCP pool for smart devices |

## DMZ (172.16.10.0/24)

Gateway: 172.16.10.1 (CARP VIP)

| IP | Device |
|----|--------|
| .1 | CARP VIP |
| .172 | two-of-clubs (UrBackup) |

---

## IP Addressing Scheme

IPs encode the card identity: `192.168.1.{suit_base + rank}`

| Suit | Base | Domain |
|------|------|--------|
| Hearts | 110 | Core Infrastructure |
| Diamonds | 130 | Productivity & Data |
| Spades | 150 | Media & Downloads |
| Clubs | 170 | Automation & AI |

**Rank mapping**: Ace=1, Two=2, Three=3, ..., Ten=10, Jack=11, Queen=12, King=13

---

## Related Documentation

- [network_topology](https://github.com/arm-ser/house-of-cards-v3/edit/master/network/network_topology.md) — Network segments and bridges
- [firewall_rules](https://github.com/arm-ser/house-of-cards-v3/blob/master/network/firewall_rules.md) — OPNsense firewall rules
- [dns_configuration](https://github.com/arm-ser/house-of-cards-v3/blob/master/network/dns_configuration.md) — Technitium DNS setup
