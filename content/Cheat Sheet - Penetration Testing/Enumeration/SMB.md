#### **SMB Enumeration**

**Commands to get smb cmd.**
```
smbclient  \\\\Target_ip\\\\USERNAME OR SHARENAME
```
```
smbclient -L //$target --option="client min protocol=core" -U ''
```

**Play with back slashes to get cmd.**
```
smbclient \\\\192.168.189.55\\shenzi\\
```

**Get cmd with port number.**
```
smbclient -N //192.168.200.105/commander -p 36445
```

**Get smb cmd.**
```
smbclient -N //192.168.189.172/DocumentsShare
```
```
smbclient //192.168.159.188/WebApp -U  CRAFT2/thecybergeek
```

**If your share name have space in  between use \**
```
smbclient //192.168.217.175/Password\ Audit -U V.Ventz%'HotelCalifornia194!'
```

**Change username, password and share accordingly to get cmd**
```
smbclient //172.16.199.21/scripts -U 'FILES//mountuser%DRtajyCwcexfiltrated.offsecbWvH/9'
```

**Command to list shares without password.**
```
smbclient -U '' -L \\\\192.168.189.55\\\\ 
```

**If SMB port open check this command to list shares.**
```
smbclient -l \\\\192.168.224.10\\:
```

##### **Smb Enumeration using SMBMAP:**
**Command to list shares**
```
smbmap -H 192.168.xxx.xxx
```

**List Shares with using Username, Password and Domain Name.**
```
smbmap -u mountuser -p 'DRtajyCwcbWvH/9'  -H 172.16.199.21 -d relia.com
```

##### **Smb Enumeration Using Enum4Linux:**
**Smb Enumeration with using Username and Password.**
```
enum4linux -a -u "CRAFT2\\thecybergeek" -p "winniethepooh" 192.168.159.188
```

**Smb Enumeration using Enum4Linux without Username and Password.**
```
enum4linux -a -u '' -p '' 192.168.159.188
```

##### **Smb Enumeration Using Crackmapexec:**
**Command to list the host which has smb service in domain or subnet.**
```
crackmapexec smb 10.10.151.0/24
```

**Command to list users of the Domain using rid brute.**
```
crackmapexec smb 192.168.189.172 -u guest -p "" --rid-brute
```

**Brute forcing on total subnet of a domain with User, NTLM hash, Password lists.**
```
crackmapexec smb 10.10.151.0/24 -u users.txt -H ntlm.hash --continue-on-success --shares
```
```
crackmapexec winrm 10.10.151.0/24 -u users.txt -p pass.txt --continue-on-success
```
