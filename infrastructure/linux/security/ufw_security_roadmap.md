*   install UFW
  
```bash
sudo apt update && sudo apt install ufw
```

- DO NOT ENABLE IT YET
- Disable and stop ufw 
  
```bash
sudo ufw disable && sudo systemctl stop ufw 
```

*   Reset all rules
  
```bash
sudo ufw reset
```

- IMPORTANT FOR DOCKER
- Setup Default Rules
  
```bash
sudo ufw default deny incoming && sudo ufw default allow outgoing
````

*   Allow SSH
  
```bash
sudo ufw allow 10220/tcp
```

----

- Enabling ==http== and ==https==
  
```bash
sudo ufw allow 80/tcp && sudo ufw allow 443/tcp
```
`

*   Allowing only ==specific IP Address==
  
```bash
sudo ufw allow from ip_address
```


*   List all ufw rules

```bash
sudo ufw status
```


***

### Notes

*   config file location is `/etc/default/ufw`
