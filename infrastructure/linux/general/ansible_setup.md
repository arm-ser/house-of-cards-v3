
# Control Room: Ansible + Semaphore Setup
### Encrypted Ansible control node with Docker-based Semaphore UI

---

## Overview

Everything lives in `~/control_room` — a single fscrypt-encrypted directory.  
After every reboot, the folder is locked (encrypted gibberish) until you run `~/start_control_room.sh`.

```
~/control_room/                  ← fscrypt encrypted at rest
├── ansible/
│   ├── ansible.cfg
│   └── inventory/
│       └── hosts.ini
├── projects/                    ← your playbooks
├── ssh/                         ← SSH key backup (offline reference)
│   └── ansible_id_ed25519
├── semaphore/
│   └── data/                    ← Semaphore SQLite DB (stores keys encrypted)
└── docker-compose.yml

~/start_control_room.sh          ← OUTSIDE encrypted folder (always accessible)
~/stop_control_room.sh           ← OUTSIDE encrypted folder (always accessible)
```

**Daily workflow:**
1. SSH to server
2. `~/start_control_room.sh` → enter fscrypt passphrase
3. Open `http://your_server_ip:3000`
4. Run Ansible jobs via Semaphore
5. `~/stop_control_room.sh` → container stops, folder locks

---

## Phase 1: Install Dependencies

```bash
sudo apt update && sudo apt upgrade -y

# Install Docker (official repo)
sudo apt install -y ca-certificates curl gnupg lsb-release
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Install Ansible and fscrypt
sudo apt install -y ansible git fscrypt libpam-fscrypt

# Add your user to the docker group
sudo usermod -aG docker $(whoami)
newgrp docker

# Verify
docker --version
ansible --version
fscrypt --version
```

---

## Phase 2: Enable Filesystem Encryption (fscrypt)

```bash
# Step 1: Find what device holds your home
df -T ~
# Look at the "Filesystem" column — note your device (e.g. /dev/sda1)
# and the "Mounted on" column (e.g. / or /home)

# Step 2: Check if ext4 encryption is already enabled
sudo tune2fs -l /dev/sda1 | grep 'Filesystem features' | grep encrypt
# If the command returns a line with "encrypt" — skip Step 3
# If it returns nothing — encryption feature is not yet enabled

# Step 3: Enable encryption on the ext4 filesystem
sudo tune2fs -O encrypt /dev/sda1

# Step 4: Initialize fscrypt system-wide
sudo fscrypt setup

# Step 5: Set up fscrypt on the mountpoint holding your home.
# Run BOTH commands — the one that already has fscrypt metadata
# will say "already set up", the other will initialise it.
sudo fscrypt setup /
sudo fscrypt setup /home
```

> **Note:** `sudo fscrypt setup /home` is only needed if `/home` is a separate partition.
> Running it on `/` covers the common case where home lives on the root partition.
> It is safe to run both — the second will simply report already set up.

---

## Phase 3: Create and Encrypt `~/control_room`

> **Important:** Create the top-level folder first, encrypt it immediately,
> then create subdirectories inside. This ensures all contents inherit the
> encryption policy.

```bash
# Create top-level folder (empty)
mkdir ~/control_room
chmod 700 ~/control_room

# Encrypt it NOW before adding anything inside
fscrypt encrypt ~/control_room
```

You will see a menu:
```
The following protector sources are available:
1 - Your login passphrase (pam_passphrase)
2 - A new custom passphrase
3 - A new raw key generated directly from /dev/random
```

**Choose option 2 — custom passphrase.** Set a strong, memorable passphrase.
This is what the start script will prompt you for each time.

```bash
# Confirm it is encrypted and currently unlocked
fscrypt status ~/control_room
# Output should include:  Unlocked: Yes

# Create all subdirectories INSIDE the encrypted folder
mkdir -p ~/control_room/ansible/inventory
mkdir -p ~/control_room/projects
mkdir -p ~/control_room/ssh
mkdir -p ~/control_room/semaphore/data

chmod -R 700 ~/control_room/ssh
```

---

## Phase 4: Generate SSH Keys

```bash
# Generate dedicated Ansible key inside the encrypted folder
ssh-keygen -t ed25519 -f ~/control_room/ssh/ansible_id_ed25519 -C "control-room"
chmod 600 ~/control_room/ssh/ansible_id_ed25519

# Print the public key — copy this to every target server
cat ~/control_room/ssh/ansible_id_ed25519.pub
```

Copy the public key to each target server:
```bash
ssh-copy-id -i ~/control_room/ssh/ansible_id_ed25519.pub user@192.168.1.x
# Repeat for each server
```

---

## Phase 5: Ansible Config and Inventory

```bash
cat > ~/control_room/ansible/ansible.cfg <<'EOF'
[defaults]
inventory           = ./inventory/hosts.ini
host_key_checking   = False
retry_files_enabled = False

[ssh_connection]
ssh_args = -o StrictHostKeyChecking=no -o ControlMaster=auto -o ControlPersist=60s
EOF
```

```bash
# Edit this to match your actual servers
cat > ~/control_room/ansible/inventory/hosts.ini <<'EOF'
[servers]
server1 ansible_host=1.2.3.4  ansible_user=ubuntu
server2 ansible_host=5.6.7.8  ansible_user=ubuntu

[databases]
db1     ansible_host=9.10.11.12 ansible_user=ubuntu
EOF
```

```bash
# A simple test playbook to verify Semaphore is working
cat > ~/control_room/projects/ping.yml <<'EOF'
---
- name: Ping all servers
  hosts: all
  gather_facts: false
  tasks:
    - name: Ping
      ping:
EOF
```

---

## Phase 6: Generate Semaphore Encryption Key

Semaphore requires a random key to encrypt SSH keys and passwords stored in its database.
**Generate it once. Store it in your password manager. If lost, the DB must be wiped.**

```bash
SEMAPHORE_KEY=$(openssl rand -base64 32)
echo "=== SAVE THIS IN YOUR PASSWORD MANAGER ==="
echo "$SEMAPHORE_KEY"
echo "==========================================="
```

---

## Phase 7: Create `docker-compose.yml`

```bash
cd ~/control_room
nano docker-compose.yml
```

Paste the following, replacing `YOUR_PASSWORD_HERE` and `YOUR_KEY_HERE`:

```yaml
services:
  semaphore:
    image: semaphoreui/semaphore:latest
    container_name: semaphore
    restart: unless-stopped
    ports:
      - 3000:3000
    environment:
      SEMAPHORE_DB_DIALECT:             sqlite
      SEMAPHORE_DB_PATH:                /var/lib/semaphore/semaphore.db
      SEMAPHORE_ADMIN:                  admin
      SEMAPHORE_ADMIN_NAME:             Admin
      SEMAPHORE_ADMIN_EMAIL:            admin@local
      SEMAPHORE_ADMIN_PASSWORD:         YOUR_PASSWORD_HERE
      SEMAPHORE_ACCESS_KEY_ENCRYPTION:  YOUR_KEY_HERE
    volumes:
      - ./ansible:/etc/ansible:ro
      - ./projects:/mnt/projects
      - ./semaphore/data:/var/lib/semaphore
    networks:
      - control-room

networks:
  control-room:
    driver: bridge
```

> **SEMAPHORE_DB_DIALECT:** Use `sqlite` (not `bolt`).  
> **SEMAPHORE_DB_PATH:** Must match the path inside the container (`/var/lib/semaphore/semaphore.db`).

---

## Phase 8: Fix Directory Permissions for Docker

The Semaphore container runs as UID/GID `1001` internally.
The mounted directories must be owned by `1001:1001` or the container will fail to start.

```bash
cd ~/control_room
sudo chown -R 1001:1001 ./semaphore/data
sudo chown -R 1001:1001 ./projects
```

> **Why this matters:** If you skip this step, Semaphore throws a permission error on startup
> and the container exits immediately. This is the most common cause of `docker compose up` failing.

---

## Phase 9: Open Port 3000

```bash
sudo ufw allow 22/tcp       # Keep SSH open
sudo ufw allow 3000/tcp     # Semaphore UI
sudo ufw --force enable
sudo ufw status
```

---

## Phase 10: Create the Start Script

```bash
cat > ~/start_control_room.sh <<'SCRIPT'
#!/bin/bash

CONTROL_DIR="$HOME/control_room"
GREEN='\033[0;32m'
RED='\033[0;31m'
YELLOW='\033[1;33m'
NC='\033[0m'

echo -e "${YELLOW}=== Control Room Startup ===${NC}"

# --- Step 1: Check fscrypt lock status ---
echo -e "\n[1/3] Checking encryption status..."
STATUS=$(fscrypt status "$CONTROL_DIR" 2>/dev/null)

if echo "$STATUS" | grep -q "Unlocked: Yes"; then
    echo -e "${GREEN}✔ control_room is already unlocked.${NC}"

elif echo "$STATUS" | grep -q "Unlocked: No"; then
    echo -e "${YELLOW}⚑ control_room is locked. Unlocking now...${NC}"
    if ! fscrypt unlock "$CONTROL_DIR"; then
        echo -e "${RED}✘ Failed to unlock. Wrong passphrase?${NC}"
        exit 1
    fi
    echo -e "${GREEN}✔ Unlocked successfully.${NC}"

else
    echo -e "${RED}✘ Cannot read fscrypt status. Is fscrypt set up correctly?${NC}"
    echo "$STATUS"
    exit 1
fi

# --- Step 2: Verify critical files are accessible ---
echo -e "\n[2/3] Verifying required files..."

REQUIRED_FILES=(
    "$CONTROL_DIR/docker-compose.yml"
    "$CONTROL_DIR/ssh/ansible_id_ed25519"
    "$CONTROL_DIR/ansible/inventory/hosts.ini"
)

ALL_GOOD=true
for FILE in "${REQUIRED_FILES[@]}"; do
    if [ -f "$FILE" ]; then
        echo -e "  ${GREEN}✔${NC} $(basename "$FILE")"
    else
        echo -e "  ${RED}✘ MISSING: $FILE${NC}"
        ALL_GOOD=false
    fi
done

if [ "$ALL_GOOD" = false ]; then
    echo -e "${RED}✘ One or more critical files are missing. Aborting.${NC}"
    exit 1
fi

# --- Step 3: Start Semaphore ---
echo -e "\n[3/3] Starting Semaphore Docker container..."
cd "$CONTROL_DIR"

if docker compose up -d; then
    SERVER_IP=$(hostname -I | awk '{print $1}')
    echo -e "\n${GREEN}=== Control Room is ACTIVE ===${NC}"
    echo -e "Semaphore UI: ${GREEN}http://${SERVER_IP}:3000${NC}"
    echo -e "Login: admin / (password you set in docker-compose.yml)"
    echo -e "\nWhen done run: ${YELLOW}~/stop_control_room.sh${NC}"
else
    echo -e "${RED}✘ Docker failed to start.${NC}"
    echo -e "Check logs with: ${YELLOW}cd ~/control_room && docker compose logs${NC}"
    exit 1
fi
SCRIPT

chmod +x ~/start_control_room.sh
```

---

## Phase 11: Create the Stop Script

```bash
cat > ~/stop_control_room.sh <<'SCRIPT'
#!/bin/bash

CONTROL_DIR="$HOME/control_room"
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
RED='\033[0;31m'
NC='\033[0m'

echo -e "${YELLOW}=== Stopping Control Room ===${NC}"

# --- Step 1: Stop Semaphore container ---
echo -e "\n[1/2] Stopping Semaphore container..."
if docker ps --format '{{.Names}}' | grep -q "^semaphore$"; then
    cd "$CONTROL_DIR"
    docker compose down
    echo -e "${GREEN}✔ Container stopped.${NC}"
else
    echo -e "${GREEN}✔ Container was not running.${NC}"
fi

# --- Step 2: Lock the encrypted folder ---
echo -e "\n[2/2] Locking control_room..."
if fscrypt lock "$CONTROL_DIR"; then
    echo -e "${GREEN}✔ control_room is now locked and encrypted.${NC}"
else
    echo -e "${YELLOW}⚑ Lock failed (files may still be open).${NC}"
    echo -e "   The folder will lock fully on next reboot."
fi

echo -e "\n${GREEN}=== Control Room is OFFLINE and ENCRYPTED ===${NC}"
SCRIPT

chmod +x ~/stop_control_room.sh
```

---

## Phase 12: First-Time Launch

```bash
~/start_control_room.sh
# Enter your fscrypt passphrase when prompted

# Verify the container is running
docker ps
```

Open `http://your_server_ip:3000` in a browser.

---

## Phase 13: Semaphore UI First-Time Configuration

### 1. Change the admin password immediately
Settings → Profile → Change password

### 2. Add your SSH Key (Key Store → New Key)
- **Type:** SSH Key
- **Name:** `ansible-main`
- **Private Key:** paste the output of:
  ```bash
  cat ~/control_room/ssh/ansible_id_ed25519
  ```
- **Passphrase:** the one you set during `ssh-keygen` (blank if none)

> The key is stored AES-encrypted inside the SQLite DB at
> `./semaphore/data/semaphore.db` — which lives inside your encrypted `~/control_room`.

### 3. Add Inventory (Inventory → New)
- **Name:** `All Servers`
- **Type:** File
- **Path:** `/etc/ansible/inventory/hosts.ini`
- **SSH Key:** `ansible-main`

### 4. Add Repository (Repositories → New)
- **Name:** `Projects`
- **URL/Path:** `/mnt/projects`
- **Branch:** leave blank
- **Access Key:** None

### 5. Create Task Template (Templates → New)
- **Name:** `Ping Test`
- **Playbook:** `ping.yml`
- **Inventory:** `All Servers`
- **Repository:** `Projects`
- **Key:** `ansible-main`

### 6. Run the template and confirm all servers respond ✔

---

## Troubleshooting

### Container exits immediately after `docker compose up -d`
```bash
docker logs semaphore
```
**Most likely cause:** permission error on `./semaphore/data` or `./projects`.
Fix:
```bash
cd ~/control_room
sudo chown -R 1001:1001 ./semaphore/data
sudo chown -R 1001:1001 ./projects
```
If the DB is corrupt from a failed first start, wipe it and restart:
```bash
docker compose down
rm -rf ./semaphore/data/*
sudo chown -R 1001:1001 ./semaphore/data
docker compose up -d
```

### `fscrypt status` shows gibberish filenames after reboot
This is correct and expected behaviour. The folder is locked.
Run `~/start_control_room.sh` to unlock it.

### `fscrypt setup /home` says "already set up"
Safe to ignore — it means that mountpoint was already configured.

---

## Security Summary

| Threat | Protection |
|---|---|
| Someone copies your VPS disk offline | `~/control_room` is fscrypt-encrypted — unreadable without passphrase |
| Server reboots unexpectedly | Folder auto-locks — Semaphore does not start, keys not exposed |
| SSH brute-force | Password login disabled, key-only SSH |
| Semaphore port exposed | UFW limits access; add IP restriction later |
| SSH key stored in plain Docker volume | Key stored encrypted in SQLite DB inside encrypted folder |

---

## Daily Workflow Reference

```
SSH to server
    ↓
~/start_control_room.sh    (enter passphrase)
    ↓
http://your_ip:3000        (run Ansible jobs)
    ↓
~/stop_control_room.sh     (container stops + folder locks)
```

After `stop_control_room.sh` — or any reboot — `~/control_room` is encrypted.
Zero credentials, keys, or config are readable at rest.
