In ProxMox Shell

Run to See all Attached Storage Devices
```bash
lsblk -o +MODEL,SERIAL
```
![[Pasted image 20231207165127.png]]
Run to see full id
```bash
ls /dev/disk/by-id
```
![[Pasted image 20231207165219.png]]

Run to pass device to TrueNas
```bash
qm set 100 -scsi0 /dev/disk/by-id/ID_FROM_PREVIOUS_COMMAND
```
- 100: Containers ID in ProxMox
- -scsia0: Increment the number with each device

EXAMPLE
```bash
qm set 100 -scsi6 /dev/disk/by-id/ata-ST8000DM004-2U9188_ZR15GERG
```
![[Pasted image 20231207172902.png]]

