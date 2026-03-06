# Proxmox Disk Passthrough Guide

## ⚠️ IMPORTANT: After adding new disk, add them to [[proxmox_queen.hrts_snapshot_script|VM 100 Snapshot and Rollback Script]] 

## 1. Find the Disk ID

List disks by their persistent ID:
```bash
ls -l /dev/disk/by-id/ | grep -v part | grep -v _1
```

Cross-reference with device names:
```bash
lsblk
```

You'll see something like:
`ata-Samsung_SSD_870_EVO_500GB_S123ABC -> ../../sdb`

---

## 2. Passthrough the Disk to a VM

Replace `<vmid>` with your VM ID and use the full by-id path:
```bash
qm set <vmid> -scsi1 /dev/disk/by-id/ata-Samsung_SSD_870_EVO_500GB_S123ABC
```

Increment the slot (`-scsi1`, `-scsi2`, etc.) to avoid conflicts.

---

## 3. Verify the Config
```bash
qm config <vmid>
```

The disk should appear under the assigned interface line.

---

## Tips

- **Always** use `/dev/disk/by-id/` — never `/dev/sdX` (changes on reboot)
- The VM should be powered off before adding disks
- Use `-virtio` instead of `-scsi` for better performance on Linux guests