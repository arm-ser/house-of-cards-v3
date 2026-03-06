# ace-of-clubs

> **Type:** VM (ID: 171) | **IP:** 192.168.1.171 | **Network:** LAN
> **Resources:** 4 vCPU, 4GB RAM, 24GB disk
> **OS:** Debian 13

---

## Role

AI and automation hub. Workflow automation, dashboards, RSS, collaborative editing. GPU passthrough planned for LLM inference.

## Services

| Service | Description |
|---------|-------------|
| n8n | Workflow automation + PostgreSQL + Redis |
| NocoDB | Database UI |
| HedgeDoc | Collaborative markdown editor + PostgreSQL |
| Homarr | Dashboard |
| CommaFeed | RSS reader |
| Cloudflared | Cloudflare tunnel |
| Portainer Agent | Connected to main Portainer |

## Planned Services (Requires GPU Passthrough)

| Service | Description |
|---------|-------------|
| Ollama | LLM inference on RTX 3090 |
| OpenWebUI | Chat interface for Ollama |

## Docker Data

`/home/user/docker/` — backed up by UrBackup on five-of-hearts

---

## Related Documentation

- [[service_matrix]] — Full service listing
