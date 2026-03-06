

### Debian 13 
- configure static ip
  
```bash
nano /etc/network/interfaces
```

```bash
auto lo
iface lo inet loopback

auto enp1s0
iface enp1s0 inet static
    address 192.168.1.100/24
    gateway 192.168.1.1
    dns-nameservers 9.9.9.9
```

```bash
sudo apt install resolvconf
```

```bash
sudo systemctl restart networking
```

- Add user to sudoers
```bash
usermod -aG sudo user
```
- install sudo
```bash
apt install sudo
```
- install curl
```bash
apt install curl
```

---

#### Edit VM or a template

```bash
 virt-customize -a /dev/zvol/rpool/data/base-500-disk-0 --install resolvconf --run-command 'systemctl enable resolvconf' --run-command 'usermod -aG docker user' --run-command 'cloud-init clean --logs'
```

---

## Step 4 — Install NFS client

```bash
sudo apt install nfs-common -y
```
## Step 5 — Verify TrueNAS NFS share allows .142

On TrueNAS web UI: Shares → NFS → `vault/nextcloud` entry must have:

- Allowed host: `192.168.1.142`
- Options: `rw`, `no_root_squash`

If not set: add it now before proceeding.
## Step 6 — Configure NFS fstab mount

```bash
sudo mkdir -p /mnt/vault/nextcloud
```

Add to `/etc/fstab`:

```bash
sudo nano /etc/fstab
```

```
192.168.1.122:/mnt/vault/nextcloud  /mnt/vault/nextcloud  nfs  hard,_netdev,nofail,rsize=131072,wsize=131072,timeo=14,retrans=3  0  0
```

Test:

```bash
mount -a
```

```bash
df -h | grep vault   
```
> should show the NFS mount

---
##### Using rsync

```bash
echo "user ALL=(ALL) NOPASSWD: /usr/bin/rsync" | sudo tee /etc/sudoers.d/rsync
```

- Then use rsync to transfer files
```bash
sudo rsync -az --info=progress2 --no-inc-recursive -e "ssh" --rsync-path="sudo rsync" /home/user/docker/jellyfin/ user@192.168.1.151:/home/user/docker/jellyfin
```

Clean up after done using

```bash 
sudo rm /etc/sudoers.d/rsync
```

```
POSTGRES_USER=n8n
POSTGRES_PASSWORD=gQC$tKwSzJCFnFaipLi4AdHUg
POSTGRES_DB=n8n
N8N_ENCRYPTION_KEY=yI+2RcuFUYmq+pNUohALWzsZT3ix70J5bHxkGfhQshQ=
N8N_USER_MANAGEMENT_JWT_SECRET=FjRn/+RGfctwQDi11Sq5PRwlbXMVq9/VBaYHpxVG1/g=
```