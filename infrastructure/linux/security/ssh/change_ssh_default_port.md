1. **Backup Your SSH Configuration (Optional):**

```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config_backup
```

2. **Edit the SSH Configuration File:**
 
```bash
sudo nano /etc/ssh/sshd_config
```

3. **Change the SSH Port:**

```bash
Port 10220
```

4. **Restart SSH Service:** 

```bash
sudo systemctl restart ssh
```
#### Optional
**Update Firewall Rules (if needed):** If you're using a firewall like UFW, you'll need to allow traffic on the new SSH port. 

```bash
sudo ufw allow 2222/tcp
```

