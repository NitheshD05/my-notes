#### **Directory Traversal**

**Using Gobuster:**
```
gobuster dir -u http://target_ip/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,aspx,cgi -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster.txt
```
```
gobuster dir -u http://target_ip/ -w /usr/share/wordlists/dirb/common.txt -t 5 -b 301 --no-error -x html,txt -o dir80.txt -r -x .xml,.php,.txt -o gobuster.txt
```
```
gobuster dir -u https://10.10.10.60 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -k -x php,html,txt,bak,conf -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster443.txt
```
Sub Domains
```
gobuster vhost --append-domain -u http://blazorized.htb -w /usr/share/seclists/Discovery/DNS/n0kovo_subdomains.txt -t 500
```

**Using Feroxbuster:**
```
feroxbuster --url http://192.168.188.99 -o feroxbuster.txt
```
```
feroxbuster --url http://192.168.188.99 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt  -o feroxbuster.txt
```

**Using dirb:**
```
dirb http://192.168.188.99 -o dirb.txt -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
```

**Using ffuf:**
```
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://192.168.188.99:8089/FUZZ -t 100 -o ffuf.txt
```
