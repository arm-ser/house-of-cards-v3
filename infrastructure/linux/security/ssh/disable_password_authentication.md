1. open config file
```bash
sudo nano /etc/ssh/sshd_config
```

2. uncomment and change PasswordAuthentication to no
```bash
PasswordAuthentication no
```

3. restart ssh service
```bash 
sudo systemctl restart ssh
```

