# two-of-clubs

> **Type:** VM (ID: 172) | **IP:** 172.16.10.172 | **Network:** DMZ
> **Resources:** 2 vCPU, 2GB RAM, 16GB disk
> **OS:** Debian 13

---

## Role

Isolated UrBackup instance in the DMZ network.

## Services

| Service | Description |
|---------|-------------|
| UrBackup | Backup service (DMZ isolated) |

## Network Notes

- Located on vmbr1 (DMZ bridge)
- OPNsense firewall rule allows NFS access to TrueNAS (.122)

---

## Related Documentation

- [[firewall_rules]] — DMZ cross-VLAN rules
- [[network_topology]] — DMZ segment
