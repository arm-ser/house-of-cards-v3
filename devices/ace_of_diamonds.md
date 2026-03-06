# ace-of-diamonds

> **Type:** VM | **IP:** 192.168.1.131 | **Network:** LAN
> **Resources:** 4 vCPU, 6GB RAM, 24GB disk
> **OS:** Debian 13

---

## Role

Secure services VM. Hosts password management, personal finance, and email bridge — all sensitive data stored on encrypted TrueNAS datasets.

## Services

| Service | Description |
|---------|-------------|
| Vaultwarden | Password manager (Bitwarden-compatible) |
| Firefly III | Personal finance manager + MariaDB + cron |
| ProtonMail Bridge | Email bridge (pending redeploy with updated image) |
| Portainer Agent | Connected to main Portainer on ten-of-hearts |

## Storage Mounts

| Mount Point | Source | Type |
|-------------|--------|------|
| vault/services | queen-of-hearts (.122) | NFS |

## Docker Data

`/home/user/docker/` — backed up by UrBackup on five-of-hearts

---

## Related Documentation

- [[queen_of_hearts]] — Encrypted vault datasets
- [[service_matrix]] — Full service listing
