```nash
nano copy_ssh_key_to_admin.sh
```

```bash
#!/bin/bash

# Check if the script is run as root
if [[ $EUID -ne 0 ]]; then
    echo "This script must be run as root."
    exit 1
fi

# Prompt for the new username
read -p "Enter the username to copy the SSH key to: " new_username

# Check if the user exists
if ! id "$new_username" &>/dev/null; then
    echo "User '$new_username' does not exist. Please create the user first."
    exit 1
fi

# Check if the user's SSH directory exists, create if not
user_ssh_dir="/home/$new_username/.ssh"
if [ ! -d "$user_ssh_dir" ]; then
    mkdir -p "$user_ssh_dir"
    chmod 700 "$user_ssh_dir"
    chown "$new_username:$new_username" "$user_ssh_dir"
fi

# Copy the SSH key from root to the user's authorized_keys file
if [ -f /root/.ssh/authorized_keys ]; then
    cp /root/.ssh/authorized_keys "$user_ssh_dir/"
    chmod 600 "$user_ssh_dir/authorized_keys"
    chown "$new_username:$new_username" "$user_ssh_dir/authorized_keys"
    echo "SSH key copied to $new_username's authorized_keys file."
else
    echo "No SSH key found in root's authorized_keys file."
fi

echo "SSH key copy process completed."
```

```bash
chmod +x copy_ssh_key_to_admin.sh
```

```bash
./copy_ssh_key_to_admin.sh
```
