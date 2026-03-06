#tool #fail2ban #server-security

1. Install fail2ban
```bash
sudo apt update
```

```bash
sudo apt-get install fail2ban
```

2. Copy the Configuration File:

```bash
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```

3. Open and edit config file
```bash
sudo nano /etc/fail2ban/jail.local
```

```bash
[sshd]
enabled = true
port = 10220
filter = sshd
logpath = /var/log/auth.log
maxretry = 10
findtime = 600
bantime = 3600
```

- `enabled`: Set to `true` to enable the SSH jail.
- `port`: The SSH service port (default is "ssh").
- `filter`: The filter to use for monitoring (default is "sshd").
- `logpath`: The path to the log file Fail2ban should monitor (default is `/var/log/auth.log`).
- `maxretry`: The maximum number of login attempts before banning an IP.
- `findtime`: The time window (in seconds) during which the maxretry value applies.
- `bantime`: The duration (in seconds) an IP address is banned.