# three-of-clubs

> **Type:** VM (ID: 173) | **IP:** 192.168.3.173 | **Network:** IoT
> **Resources:** 2 vCPU, 2GB RAM, 16GB disk

---

## Role

Home Assistant OS VM on the IoT network. Manages smart home devices with no internet access.

## Services

| Service | Description |
|---------|-------------|
| Home Assistant OS | Smart home automation platform |
| Matter Server | Smart home protocol support |
| Homebridge | HomeKit bridge |

## Network Notes

- Located on vmbr2 (IoT bridge)
- IoT network has **no internet access**
- Home Assistant communicates with IoT devices on 192.168.3.0/24

---

## Related Documentation

- [[firewall_rules]] — IoT isolation rules
- [[network_topology]] — IoT segment
