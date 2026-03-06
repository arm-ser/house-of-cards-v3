# queen-of-diamonds

> **Type:** VM (ID: 142) | **IP:** 192.168.1.142 | **Network:** LAN
> **Resources:** 4 vCPU, 6GB RAM, 24GB disk
> **OS:** Debian 13

---

## Role

Dedicated Nextcloud VM with OnlyOffice for document editing. All user files stored on encrypted TrueNAS dataset.

## Services

| Service | Description |
|---------|-------------|
| Nextcloud | File sync and sharing + MariaDB |
| OnlyOffice | Document editing (pending connection to Nextcloud) |
| Portainer CE | Standalone container management |

## Storage Mounts

| Mount Point | Source | Type |
|-------------|--------|------|
| vault/nextcloud | queen-of-hearts (.122) | NFS |

## Docker Data

`/home/user/docker/` — backed up by UrBackup on five-of-hearts

---

## Related Documentation

- [[queen_of_hearts]] — Encrypted vault datasets
