
# NEW VMS FROM TEMPLATE 500 OR ALREADY MODIFIED OLD VMS

### Future resize workflow for any VM cloned from this template

```
qm resize <vmid> scsi0 +XG
```

Then inside the VM:

```
sudo growpart /dev/sda 1 && sudo resize2fs /dev/sda1
```

# OLD VMS

Step 1: 
- ## ==RESIZE VM DISK FROM PROXMOX BEFORE CONTINUING==

```bash
sudo fdisk -l /dev/sda
```

IF LAYOUT IS LIKE THIS

```bash
Disk /dev/sda: 32 GiB, 34359738368 bytes, 67108864 sectors
Disk model: QEMU HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xb4309755

Device     Boot    Start      End  Sectors  Size Id Type
/dev/sda1  *        2048 31719423 31717376 15.1G 83 Linux
/dev/sda2       31721470 33552383  1830914  894M  f W95 Ext'd (LBA)
/dev/sda5       31721472 33552383  1830912  894M 82 Linux swap / Solaris
```

CONTINUE

```bash
sudo swapoff -a
```

```bash
sudo fdisk /dev/sda
```

```
d
```

```
5
```

```
d
```

```
2
```

```
w
```

```bash
sudo apt install parted -y
```

```bash
sudo parted /dev/sda resizepart 1 100%
```

```bash
sudo resize2fs /dev/sda1
```

```
sudo nano /etc/fstab
```

## ==CRITICAL==: Comment out swap uuid line

```bash
sudo nano /etc/fstab
```

```bash
UUID=6a274047-c046-42c6-a404-03ad04e7ea91 none  swap  sw       0       0
```

REBOOT 

```bash
sudo fallocate -l 894M /swapfile
```

```bash
sudo chmod 600 /swapfile
```

```bash
sudo mkswap /swapfile
```

```bash
sudo swapon /swapfile
```

```bash
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```