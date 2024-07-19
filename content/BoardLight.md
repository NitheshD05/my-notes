**Ping**
![[Pasted image 20240708195531.png]]

Nmap Scans
```
mapit 10.10.11.11
```

Nmap Default Scan
![[Pasted image 20240708195647.png]]

Nmap Script and Service Scan
![[Pasted image 20240708195710.png]]

Nmap All Port Scan
![[Pasted image 20240708195805.png]]

On Port 80 http://10.10.11.11
![[Pasted image 20240708195839.png]]
Nothing this website, so will enumerate sub domains

Using ffuf
```
ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -u http://board.htb -H "Host: FUZZ.board.htb" -fs 15949
```
![[Pasted image 20240708201448.png]]
We found a new sub domain crm.board.htb

Updated my hosts file
![[Pasted image 20240708201742.png]]

On http://crm.board.htb
![[Pasted image 20240708201824.png]]
Can see version of the web server Dolibarr 17.0.0, will search for public exploits

Just used default password admin:admin it worked :)
![[Pasted image 20240708201939.png]]

Found POC on how to get reverse shell as www-data in this link https://github.com/dollarboysushil/Dolibarr-17.0.0-Exploit-CVE-2023-30253

Create a website
![[Pasted image 20240708203422.png]]

![[Pasted image 20240708203441.png]]

![[Pasted image 20240708203501.png]]
Create it.....!

Now create a page
![[Pasted image 20240708203613.png]]

![[Pasted image 20240708203650.png]]

Then just follow the instruction on the link I have provide to get reverse shell...!
Its not a long process, but lazy to capture SS 


Will Jump to Initial Foothold.....!



Shell as www-data
![[Pasted image 20240708204035.png]]

Don't have permission to open user directory
![[Pasted image 20240708204121.png]]

Found db pass in /var/www/html/crm.board.htb/htdocs/conf/conf.php
![[Pasted image 20240708205404.png]]

There is no db port in this machine, so will spray this password to the user larissa
![[Pasted image 20240708205815.png]]
Now we have session as Larissa...!

User.txt
![[Pasted image 20240708210007.png]]



Will jump to Privilege Escalation....!

We don't have any sudo privileges
![[Pasted image 20240708210111.png]]

We can see lot of unknown SUID binaries, will check that
![[Pasted image 20240708210731.png]]

Found exploit for this unknown SUID binary https://github.com/MaherAzzouzi/CVE-2022-37706-LPE-exploit/blob/main/exploit.sh
I have saved this exploit in exp.sh
![[Pasted image 20240708211744.png]]

And executed it and I'm R0ot now
![[Pasted image 20240708211817.png]]

**Root.txt**
![[Pasted image 20240708211854.png]]


Done with BoardLight......:)
