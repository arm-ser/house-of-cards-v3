# jack-of-diamonds

> **Type:** VM | **IP:** 192.168.1.141 | **Network:** LAN
> **Resources:** 1 vCPU, 512MB RAM, 8GB disk
> **Status:** Planned

---

## Role

Management VM with encrypted disk. Ansible/Semaphore for infrastructure automation. On-demand — can be shut down when not in use.

## Planned Services

| Service | Description |
|---------|-------------|
| Ansible | Configuration management |
| Semaphore | Ansible UI |

## Storage Mounts

| Mount Point | Source | Type |
|-------------|--------|------|
| vault/config | queen-of-hearts (.122) | NFS |

## Security

- Proxmox LUKS encrypted disk
- SSH key store for all VMs
- fscrypt encrypted control room

---

## Related Documentation

- [[ansible_setup]] — Ansible + Semaphore setup
