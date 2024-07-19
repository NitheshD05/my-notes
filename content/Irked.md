**Ping**
![[Pasted image 20240704091041.png]]

**Nmap Scans**
```
mapit 10.10.10.117
```

Nmap Default Scan
![[Pasted image 20240704091054.png]]

Nmap Service and Script Scan
![[Pasted image 20240704092659.png]]

Nmap All Port Scan
![[Pasted image 20240704092814.png]]

Nmap Service and Script Scan for New ports
![[Pasted image 20240704092946.png]]

On Port 80 http://10.10.10.117
![[Pasted image 20240704091131.png]]

Gobuster Scan
```
gobuster dir -u http://10.10.10.117 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,aspx -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster.txt
```
![[Pasted image 20240704100954.png]]

This Look's like IRC exploit, there are 4 open UnrealIRCD ports, will check for public exploits


Found a public exploit https://github.com/Ranger11Danger/UnrealIRCd-3.2.8.1-Backdoor/blob/master/exploit.py

Have saved the program in file named exp.py and modified a bit with my local ip and listening port for reverse shell then executed it 
![[Pasted image 20240704121340.png]]

First i have tried with port 6697 and it worked
```
python3 exp.py -payload bash 10.10.10.117 6697
```
![[Pasted image 20240704121403.png]]

![[Pasted image 20240704121551.png]]

Can't read user.txt as ircd
![[Pasted image 20240704121700.png]]

There is a password in .Documents directory
![[Pasted image 20240704123532.png]]

Will try this password for djmardov
![[Pasted image 20240704123615.png]]
Didn't work.

Steg is image content extractor, will try the password to extract hidden info from the image, But i can't find any image in the machine. So will try with image in the website
![[Pasted image 20240704123754.png]]

I have downloaded the image and extracted the hidden content using Steghide, use the link to understand Steghide usage https://medium.com/the-kickstarter/steganography-on-kali-using-steghide-7dfd3293f3fa 
```
steghide extract -sf irked.jpg
```
![[Pasted image 20240704124309.png]]

Will use this password to switch as djmardov
```
su djmardov
```
![[Pasted image 20240704124444.png]]

**User.txt**
![[Pasted image 20240704124844.png]]



Will Jump to Privilege Escalation......!

Actually We can directly escalate our Privilege to root with the help of viewuser without getting access to DJmardov, because viewuser have SUID assigned.
![[Pasted image 20240704125551.png]]

So checked how it works, and found a way to escalate my privilege
![[Pasted image 20240704125827.png]]
It is checking for /tmp/listuser and it is not found, will check /tmp dir

So there is no listusers dir
![[Pasted image 20240704125911.png]]

Will create one
![[Pasted image 20240704130006.png]]
Now created a file and added the user name djmardov, it says permission denied, will change the permissions.

Still not worked as expected
![[Pasted image 20240704130145.png]]

What if it execute the command, lets insert "id"
![[Pasted image 20240704130232.png]]
Worked finally....!

We can add "su" to it and escalate our privilege
![[Pasted image 20240704130328.png]]
w3 Ar3 r0o1.....!

**root.txt**
![[Pasted image 20240704130458.png]]




Done with Irked.....:)

