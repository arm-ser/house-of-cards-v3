
```bash
ssh-keygen -t ed25519 -a 120
```
==Ed25519== : scheme
==- a==  : rounds of key derivations

- Overwrite (y/n)? - y
- Enter passphrase (empty for no passphrase): ```<Enter your secure passphrase>```

The key will be saved in your home directory
```
ls /home/<USERNAME>/.ssh
```

----
- [What are ssh-keygen best practices?](https://overflow.projectsegfau.lt/exchange/security/questions/143442/what-are-ssh-keygen-best-practices)
