```bash
nano create_user.sh
```

```bash
#!/bin/bash

# Check if the script is run with root privileges
if [ "$EUID" -ne 0 ]; then
    echo "Please run this script as root."
    exit 1
fi

# Prompt for username
read -p "Enter the new username: " username

# Check if the username already exists
if id "$username" &>/dev/null; then
    echo "Username '$username' already exists. Please choose a different username."
    exit 1
fi

# Prompt for the user's full name
read -p "Enter the full name of the user: " full_name

# Prompt for the user's password
read -sp "Enter a password for the user: " password
echo

# Create the user
useradd -m -c "$full_name" -s /bin/bash "$username"

# Set the password for the user
echo "$username:$password" | chpasswd

# Add the user to the sudo group
usermod -aG sudo "$username"

# Display user creation information
echo "User '$username' has been created and added to the sudo group."

# Optionally, you can add additional user configuration steps here.

exit 0
```

```bash
chmod +x create_user.sh
```

```bash
sudo ./create_user.sh
```

