# ten-of-hearts

> **Type:** LXC | **IP:** 192.168.1.120 | **Network:** LAN
> **Resources:** 1 vCPU, 1GB RAM, 4GB disk

---

## Role

Reverse proxy and container management hub. NPMPlus handles SSL termination and routing; Portainer manages Docker across 3 agent VMs.

## Services

| Service | Description |
|---------|-------------|
| NPMPlus | Reverse proxy with Certbot SSL (*.octachainlabs.com) |
| Portainer | Container management — 3-agent license |

## Portainer Agents

| Agent | VM | IP |
|-------|----|----|
| Agent 1 | ace-of-diamonds | .131 |
| Agent 2 | ace-of-spades | .151 |
| Agent 3 | ace-of-clubs | .171 |

## High Availability

Proxmox HA auto-restart enabled for this LXC.

---

## Related Documentation

- [[dns_configuration]] — Proxy host entries
- [[service_matrix]] — All proxied services
