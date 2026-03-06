1.  Open SSH configuration file with text editor

```bash
sudo nano /etc/ssh/sshd_config
```

1.  Change PermitRootLogin to no

```bash
PermitRootLogin no
```

```bash
sudo systemctl restart ssh
```
