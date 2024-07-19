**Ping**
```
ping 10.10.10.8
```
![[Pasted image 20240615113810.png]]

Nmap default Scan
```
nmap 10.10.10.8 -oN dnmap
```
![[Pasted image 20240615114043.png]]
W just have port 80

Nmap all Port Scan
```
nmap -p- --min-rate 10000 10.10.10.8 -oN inmap
```
![[Pasted image 20240615114251.png]]

Nmap Script and Service Scan
```
nmap -sCV 10.10.10.8 -oN snmap 
```
![[Pasted image 20240615114321.png]]
We have the service Info know, lets look for any public exploit for that.




Found an public exploit for the server running on the target https://github.com/randallbanner/Rejetto-HTTP-File-Server-HFS-2.3.x---Remote-Command-Execution/blob/main/hfs-2-3-exploit.py
![[Pasted image 20240615115911.png]]

Will run the exploit against our Target, Firstly i have saved the exploit code in a file named exp.py
```
python2 exp.py
```
![[Pasted image 20240615120013.png]]
Have to enter the local ip and listening port, remote ip and port, also have to set local listening **port to 80**, to get a shell, because no other port works, this might be due to any firewall restriction on the target.

Got the user.txt 
![[Pasted image 20240615120330.png]]

Will move to Privilege Escalation....!



Our Privileges
```
whoami /priv
```
![[Pasted image 20240615120504.png]]

Users in the Machine
```
net user
```
![[Pasted image 20240615120559.png]]

User Group
```
net user kostas
```
![[Pasted image 20240615120642.png]]
So we are no a part of any interesting Group.

Lets check system info
```
systeminfo | findstr /B /C:"OS Name" /C:"OS Version"
```
![[Pasted image 20240615125338.png]]
Its look like an kernel exploit.

Will search for the public  Exploit for the kernel version
![[Pasted image 20240615125728.png]]
Found one https://www.exploit-db.com/exploits/41020
binary https://gitlab.com/exploit-database/exploitdb-bin-sploits/-/raw/main/bin-sploits/41020.exe

Will transfer this binary to the target machine and exploit it.

**Note: I have tested this binary in Powershell environment, it doesn't work and can't do privilege escalation, so I have used exploit-dB's exploit for initial foothold, because it gives us a normal cmd shell.**
**Link to the exploit for Initial Foothold: https://www.exploit-db.com/exploits/39161**
**In the python script change the local ip and port, set local port to 4444**
**Now Will get a normal cmd shell now.**

After transferring the binary, will run it
![[Pasted image 20240615130305.png]]
Cooool....!

Got root.txt in Admin's Desktop
![[Pasted image 20240615130404.png]]


Done with Optimum.....:) 


