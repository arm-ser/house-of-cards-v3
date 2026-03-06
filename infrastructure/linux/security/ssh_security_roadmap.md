1.  SSH to the server as root
    1.  `ssh root@server_ip_or_hostname`

2.  Create Non-root Admin User (with sudo privileges)
    1.  [[create_non_root_user|Create Non Root User]]
    2.  ==IMPORTANT== : if you already uploaded your ssh key to the server (from cloud provider or github), use [[copy_ssh_key_to_admin|Copy SSH Key to Admin]] script to transfer it to your Admin user and ==SKIP Step 4==

3.  Close SSH connection
    1.  `exit`

4.  Create SSH key and copy it to the server (Skip if you already uploaded your key to the server)
    1.  [[generate_ssh_keys|Generate SSH Keys]]
    2.  [[copy_ssh_key_to_server|Copy SSH Key to Server]]

5.  SSH to the server as the Admin user you just created
    1.  `ssh username@server_ip_or_hostname`

6.  Disable remote authentication for root
    1.  [[disable_root_authentication|Disable root Authentication]]

7.  Disable root account
    1.  [[disable_root_account|Disable root Account]]

8.  Disable password authentication
    1.  [[disable_password_authentication|Disable Password Authentication]]

9.  Change SSH default port
    1.  [[change_ssh_default_port|Change SSH Default Port]]

10. Install and Configure fail2ban to prevent bruteforce attacks
    1.  [[install_configure_fail2ban|Install and Configure fail2ban]]

11. Close SSH connection and open it again to check if everything works correctly
    1.  `ssh -p 10220 username@server_ip_or_hostname`

***

su - switch user
