# Network Topology

Overview of the 5 network segments and Proxmox virtual bridges.

---

## Network Segments

| Segment | Subnet | Bridge | VLAN | Gateway (CARP VIP) | Purpose |
|---------|--------|--------|------|---------------------|---------|
| LAN | 192.168.1.0/24 | vmbr0 | — | .1 | Servers and infrastructure |
| DMZ | 172.16.10.0/24 | vmbr1 | — | .1 | Isolated services |
| IoT | 192.168.3.0/24 | vmbr2 | — | .1 | Smart home devices (no internet) |
| GUST | 192.168.147.0/24 | vmbr3 | — | .1 | Family/guest WiFi |
| WAN | — | vmbr4 | — | — | Internet uplink |

## Physical NIC Allocation

OPNsense VM (king-of-hearts) has PCIe 4-port NIC passed through:

| PCIe Port | Network | Connects To |
|-----------|---------|-------------|
| Port 1 | WAN | ISP modem (Arris TM1602A) |
| Port 2 | LAN | 8-port managed switch (TP-Link TL-SG108E) |
| Port 3 | GUST | GUST WiFi AP (Netgear AX1800) |
| Port 4 | IoT | IoT WiFi AP (Netgear R7900P, AP mode) |

**Onboard NIC** → LAN switch (Proxmox management + VM traffic on vmbr0)

## Proxmox Virtual Bridges

| Bridge | Subnet | Physical Uplink | Purpose |
|--------|--------|-----------------|---------|
| vmbr0 | 192.168.1.0/24 | Onboard NIC → LAN switch | All VMs/LXCs + Proxmox management |
| vmbr1 | 172.16.10.0/24 | None (internal) | DMZ — OPNsense routes to physical DMZ |
| vmbr2 | 192.168.3.0/24 | None (internal) | IoT — OPNsense routes to physical IoT port |
| vmbr3 | 192.168.147.0/24 | None (internal) | GUST — OPNsense routes to physical GUST port |

## Traffic Flow

```
Internet → Modem → PCIe Port 1 (WAN)
                        |
             [king-of-hearts — OPNsense VM]
             /       |         |          \
      PCIe-2    PCIe-3     PCIe-4    virtio NICs
       LAN       GUST       IoT     (vmbr0,1,2,3)
        |          |          |           |
    8-port      GUST AP    IoT AP    Internal bridges
    switch    (AX1800)   (R7900P)      |
        |                           +-- vmbr0: all VMs/LXCs
     [vmbr0 via onboard NIC]        +-- vmbr1: two-of-clubs (DMZ)
        |                           +-- vmbr2: three-of-clubs (IoT)
     All server VMs + LXCs          +-- vmbr3: (future GUST VMs)
```

VMs on vmbr0 reach the internet via: vmbr0 → LAN switch → OPNsense PCIe port 2 (LAN) → OPNsense routes → PCIe port 1 (WAN) → modem.

## CARP Failover

| VIP | Network | Primary (king-of-hearts) | Backup (wall-of-cards) |
|-----|---------|-------------------------|----------------------|
| 192.168.1.1 | LAN | .123 | .3 |
| 192.168.147.1 | GUST | king-of-hearts | wall-of-cards |
| 192.168.3.1 | IoT | king-of-hearts | wall-of-cards |
| 172.16.10.1 | DMZ | king-of-hearts | wall-of-cards |

All DHCP scopes push the CARP VIP (.1) as default gateway. Automatic failover if primary goes down.

## Switch Ports (8-port managed — LAN)

| Port | Device |
|------|--------|
| 1 | ace-of-hearts onboard NIC (Proxmox + VMs) |
| 2 | king-of-hearts PCIe port 2 (OPNsense LAN) |
| 3 | wall-of-cards (backup OPNsense) |
| 4 | Raspberry Pi (backup DNS) |
| 5 | ten-of-spades LAN NIC (gaming PC) |
| 6-8 | Available |

## Physical Network Devices

| Device | Model | Role | Network |
|--------|-------|------|---------|
| ISP Modem | Arris TM1602A | Internet gateway | WAN |
| LAN Switch | TP-Link TL-SG108E | 8-port managed switch | LAN |
| GUST AP | Netgear AX1800 | WiFi 6 access point | GUST |
| IoT AP | Netgear R7900P | AP mode for smart devices | IoT |
| Spare Switch | TP-Link TL-SG105E | 5-port (stored) | — |
| Spare Switch | TP-Link TL-SG116 | 16-port (stored) | — |

---

## Related Documentation

- [firewall_rules](https://github.com/arm-ser/house-of-cards-v3/blob/master/network/firewall_rules.md) — OPNsense firewall rules
- [dhcp_configuration](https://github.com/arm-ser/house-of-cards-v3/blob/master/network/dhcp_configuration.md) — Static IP mappings
- [dns_configuration](https://github.com/arm-ser/house-of-cards-v3/blob/master/network/dns_configuration.md) — Technitium DNS setup
- [service_matrix](https://github.com/arm-ser/house-of-cards-v3/blob/master/network/service_matrix.md) — Service-to-VM mapping
