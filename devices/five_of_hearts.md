# five-of-hearts

> **Type:** LXC | **IP:** 192.168.1.115 | **Network:** LAN
> **Resources:** 1 vCPU, 512MB RAM, 8GB disk
> **OS:** Debian 13

---

## Role

Centralized UrBackup server. Backs up `/home/user/docker/` directories from all Docker VMs.

## Services

| Service | Description |
|---------|-------------|
| UrBackup Server | File and image backup for all Docker VMs |

## Storage Mounts

| Mount Point | Source | Type |
|-------------|--------|------|
| vault/docker | queen-of-hearts (.122) | NFS |
| bulk/backup/docker | queen-of-hearts (.122) | NFS |

## Backup Targets

| Source VM | What's Backed Up |
|-----------|-----------------|
| ace-of-diamonds (.131) | /home/user/docker/* |
| queen-of-diamonds (.142) | /home/user/docker/* |
| king-of-diamonds (.143) | /home/user/docker/* |
| ace-of-spades (.151) | /home/user/docker/* |
| ace-of-clubs (.171) | /home/user/docker/* |

---

## Related Documentation

- [[queen_of_hearts]] — Storage pools and NFS shares
