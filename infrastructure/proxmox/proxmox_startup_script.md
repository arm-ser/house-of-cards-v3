
```bash
nano /usr/local/bin/proxmox-startup.sh
```

```bash
#!/bin/bash

# ============================================================
#  Proxmox Startup — Manual trigger (Stage 2 + 3)
#
#  Stage 2  → TrueNAS (VM 100) + independent guests in parallel
#             Waits until NAS ping + NFS exports are healthy
#
#  Stage 3  → NAS-dependent guests
# ============================================================

export PATH="/usr/sbin:/usr/bin:/sbin:/bin"

NAS_VM=100;  NAS_IP="192.168.1.122"

STAGE2_VMS=(245 250 173 172 131)
STAGE2_CTS=(112 113)

STAGE3_VMS=(142 143 151 171)
STAGE3_CTS=(115 120)

RETRY_INTERVAL=2

# ─── Helpers ─────────────────────────────────────────────────

log() { echo "[$(date '+%H:%M:%S')] $*"; }

start_vm() {
    local id=$1
    if qm status "$id" 2>/dev/null | grep -q "running"; then
        log "VM $id already running — skipping."
    else
        log "Starting VM $id..."
        qm start "$id"
    fi
}

start_ct() {
    local id=$1
    if pct status "$id" 2>/dev/null | grep -q "running"; then
        log "CT $id already running — skipping."
    else
        log "Starting CT $id..."
        pct start "$id"
    fi
}

wait_for_ping() {
    local ip=$1 label=$2 elapsed=0
    log "Waiting for $label ($ip) to respond to ping..."
    until ping -c1 -W2 "$ip" &>/dev/null; do
        sleep "$RETRY_INTERVAL"
        elapsed=$((elapsed + RETRY_INTERVAL))
        log "  ... still waiting for $label (${elapsed}s)"
    done
    log "✔ $label is reachable."
}

wait_for_nfs() {
    local elapsed=0
    log "Waiting for NFS exports on $NAS_IP (showmount -e)..."
    until showmount -e "$NAS_IP" &>/dev/null; do
        sleep "$RETRY_INTERVAL"
        elapsed=$((elapsed + RETRY_INTERVAL))
        log "  ... still waiting for NFS (${elapsed}s)"
    done
    log "✔ NFS exports available."
}

# ─── Preflight checks ────────────────────────────────────────

if ! command -v showmount &>/dev/null; then
    log "ERROR: 'showmount' not found. Install it with: apt install nfs-common"
    exit 1
fi

if ! command -v qm &>/dev/null || ! command -v pct &>/dev/null; then
    log "ERROR: 'qm' or 'pct' not found. Is this running on a Proxmox host?"
    exit 1
fi

# ─── Startup Sequence ────────────────────────────────────────

log "════════════════════════════════════════"
log " Manual startup — $(date)"
log "════════════════════════════════════════"

# ── Stage 2: TrueNAS + independent guests ────────────────────
log ""
log "[ Stage 2 ] Starting TrueNAS + independent guests"

start_vm $NAS_VM
for id in "${STAGE2_VMS[@]}"; do start_vm "$id"; done
for id in "${STAGE2_CTS[@]}"; do start_ct "$id"; done

log "All Stage 2 guests started. Waiting for NAS to become healthy..."
wait_for_ping "$NAS_IP" "TrueNAS"
wait_for_nfs

# ── Stage 3: NAS-dependent guests ────────────────────────────
log ""
log "[ Stage 3 ] Starting NAS-dependent guests"

for id in "${STAGE3_VMS[@]}"; do start_vm "$id"; done
for id in "${STAGE3_CTS[@]}"; do start_ct "$id"; done

log ""
log "════════════════════════════════════════"
log " Startup complete!"
log "════════════════════════════════════════"
```

```bash
chmod +x /usr/local/bin/proxmox-startup.sh
```

```bash
echo "alias startup='/usr/local/bin/proxmox-startup.sh'" >> ~/.bashrc
```

```bash
source ~/.bashrc
```