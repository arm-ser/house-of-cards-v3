# king-of-diamonds

> **Type:** VM (ID: 143) | **IP:** 192.168.1.143 | **Network:** LAN
> **Resources:** 4 vCPU, 6GB RAM, 24GB disk
> **OS:** Debian 13

---

## Role

Dedicated Immich VM for photo and video management with machine learning capabilities.

## Services

| Service | Description |
|---------|-------------|
| Immich Server | Photo/video management |
| Immich ML | Machine learning (face detection, smart search) |
| PostgreSQL + pgvector | Immich database |
| Redis | Immich cache |
| Portainer CE | Standalone container management |

## Storage Mounts

| Mount Point | Source | Type |
|-------------|--------|------|
| gallery/gallery | queen-of-hearts (.122) | NFS |

## Docker Data

`/home/user/docker/` — backed up by UrBackup on five-of-hearts

---

## Related Documentation

- [[queen_of_hearts]] — Gallery pool
- [[fix_immich_file_size_limit_nginx_proxy_manager]] — NPMPlus file size fix
