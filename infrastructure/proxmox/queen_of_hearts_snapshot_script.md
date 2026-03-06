

# Snapshot

**Put it in `/usr/local/bin/`** — this is the standard location for custom admin scripts on Linux/Proxmox:


> Save the script
```bash
nano /usr/local/bin/vm100-snapshot.sh
```

> Make it executable
```bash
chmod +x /usr/local/bin/vm100-snapshot.sh
```

> Run it from anywhere on the host simply as:
```bash
vm100-snapshot.sh
```

```bash
#!/bin/bash

VMID=100
SNAPSHOT_NAME="snap_$(date +%Y%m%d_%H%M%S)"
SNAPSHOT_DESC="Auto snapshot - passthrough disks temporarily removed"

# --- ADD/REMOVE PASSTHROUGH DISKS HERE ---
declare -A PASSTHROUGH_DISKS=(
    ["scsi1"]="/dev/disk/by-id/ata-Samsung_SSD_850_EVO_500GB_S2RANB0HB28478D"
    ["scsi2"]="/dev/disk/by-id/ata-Samsung_SSD_850_EVO_500GB_S3PTNF0J949611E"
)
# -----------------------------------------

# Check VM is not running
STATUS=$(qm status $VMID | awk '{print $2}')
if [ "$STATUS" == "running" ]; then
    echo "ERROR: VM $VMID is running. Stop it before snapshotting."
    exit 1
fi

# Save full original config lines before detaching
# e.g. "scsi1: /dev/disk/by-id/...,size=476940K"
declare -A ORIGINAL_CONFIG
for SLOT in "${!PASSTHROUGH_DISKS[@]}"; do
    # Extract everything after "scsi1: " — preserves size and any extra options
    ORIGINAL_CONFIG[$SLOT]=$(qm config $VMID | grep "^${SLOT}:" | sed "s/^${SLOT}: //")
    if [ -z "${ORIGINAL_CONFIG[$SLOT]}" ]; then
        echo "WARNING: Could not read config for $SLOT — will use path from script on re-attach"
    else
        echo "==> Saved config for $SLOT: ${ORIGINAL_CONFIG[$SLOT]}"
    fi
done

# Detach disks (removes from config only, does NOT touch physical disk)
echo "==> Detaching passthrough disks from VM $VMID..."
for SLOT in "${!PASSTHROUGH_DISKS[@]}"; do
    echo "    Detaching $SLOT..."
    # NOTE: --delete uses double dash; disk slot args use single dash
    qm set $VMID --delete $SLOT
    if [ $? -ne 0 ]; then
        echo "ERROR: Failed to detach $SLOT. Aborting."
        exit 1
    fi
done

# Take snapshot
echo "==> Taking snapshot: $SNAPSHOT_NAME"
qm snapshot $VMID $SNAPSHOT_NAME --description "$SNAPSHOT_DESC"
SNAP_STATUS=$?

# Re-attach disks regardless of snapshot result
echo "==> Re-attaching passthrough disks..."
for SLOT in "${!PASSTHROUGH_DISKS[@]}"; do
    CONFIG_VALUE="${ORIGINAL_CONFIG[$SLOT]}"
    if [ -z "$CONFIG_VALUE" ]; then
        # Fall back to bare path from the script definition
        CONFIG_VALUE="${PASSTHROUGH_DISKS[$SLOT]}"
    fi
    echo "    Attaching $SLOT -> $CONFIG_VALUE"
    # IMPORTANT: disk slot assignment uses a single dash (e.g. -scsi1), NOT --scsi1
    qm set $VMID -${SLOT} "$CONFIG_VALUE"
    if [ $? -ne 0 ]; then
        echo "ERROR: Failed to re-attach $SLOT! Check 'qm config $VMID' manually."
    fi
done

# Report final result
if [ $SNAP_STATUS -eq 0 ]; then
    echo "==> Snapshot successful: $SNAPSHOT_NAME"
else
    echo "==> ERROR: Snapshot failed!"
    exit 1
fi

echo "==> Verify with: qm config $VMID"
echo "==> List snapshots with: qm listsnapshot $VMID"
```

# Rollback

Save it as `/usr/local/bin/vm100-rollback.sh` and `chmod +x` it the same way.

##### Usage

> First, find your snapshot name
```bash
qm listsnapshot 100
```

> Then roll back
```
vm100-rollback.sh snap_20250222_143012
```

##### Creation

> Save the script
```bash
nano /usr/local/bin/vm100-rollback.sh
```

> Make it executable
```bash
chmod +x /usr/local/bin/vm100-rollback.sh
```

```bash
#!/bin/bash

VMID=100
SNAPSHOT_NAME="${1}"  # Pass snapshot name as argument

# --- SAME AS YOUR SNAPSHOT SCRIPT ---
declare -A PASSTHROUGH_DISKS=(
    ["scsi1"]="/dev/disk/by-id/ata-Samsung_SSD_850_EVO_500GB_S2RANB0HB28478D"
    ["scsi2"]="/dev/disk/by-id/ata-Samsung_SSD_850_EVO_500GB_S3PTNF0J949611E"
)
# -------------------------------------

# Require snapshot name argument
if [ -z "$SNAPSHOT_NAME" ]; then
    echo "Usage: vm100-rollback.sh <snapshot_name>"
    echo "List snapshots with: qm listsnapshot $VMID"
    exit 1
fi

# Check VM is not running
STATUS=$(qm status $VMID | awk '{print $2}')
if [ "$STATUS" == "running" ]; then
    echo "ERROR: VM $VMID is running. Stop it before rolling back."
    exit 1
fi

# Save current config for passthrough disks before detaching
declare -A ORIGINAL_CONFIG
for SLOT in "${!PASSTHROUGH_DISKS[@]}"; do
    ORIGINAL_CONFIG[$SLOT]=$(qm config $VMID | grep "^${SLOT}:" | sed "s/^${SLOT}: //")
    if [ -z "${ORIGINAL_CONFIG[$SLOT]}" ]; then
        echo "WARNING: Could not read config for $SLOT — will fall back to path from script"
    else
        echo "==> Saved config for $SLOT: ${ORIGINAL_CONFIG[$SLOT]}"
    fi
done

# Detach passthrough disks
echo "==> Detaching passthrough disks from VM $VMID..."
for SLOT in "${!PASSTHROUGH_DISKS[@]}"; do
    echo "    Detaching $SLOT..."
    qm set $VMID --delete $SLOT
    if [ $? -ne 0 ]; then
        echo "ERROR: Failed to detach $SLOT. Aborting."
        exit 1
    fi
done

# Rollback
echo "==> Rolling back VM $VMID to snapshot: $SNAPSHOT_NAME"
qm rollback $VMID $SNAPSHOT_NAME
ROLLBACK_STATUS=$?

# Re-attach disks regardless of rollback result
echo "==> Re-attaching passthrough disks..."
for SLOT in "${!PASSTHROUGH_DISKS[@]}"; do
    CONFIG_VALUE="${ORIGINAL_CONFIG[$SLOT]}"
    if [ -z "$CONFIG_VALUE" ]; then
        CONFIG_VALUE="${PASSTHROUGH_DISKS[$SLOT]}"
    fi
    echo "    Attaching $SLOT -> $CONFIG_VALUE"
    qm set $VMID -${SLOT} "$CONFIG_VALUE"
    if [ $? -ne 0 ]; then
        echo "ERROR: Failed to re-attach $SLOT! Check 'qm config $VMID' manually."
    fi
done

# Report
if [ $ROLLBACK_STATUS -eq 0 ]; then
    echo "==> Rollback successful to: $SNAPSHOT_NAME"
else
    echo "==> ERROR: Rollback failed!"
    exit 1
fi

echo "==> Verify with: qm config $VMID"
```

