![[Pasted image 20240720111329.png]]

**Ping**
![[Pasted image 20240702122612.png]]

Nmap Scans
```
mapit 10.10.10.88
```

Nmap Default Scan
![[Pasted image 20240702122716.png]]

Nmap Service and Script Scan
![[Pasted image 20240702122832.png]]

Nmap All Port Scan
![[Pasted image 20240702124135.png]]

Gobuster Scan
```
gobuster dir -u http://10.10.10.88 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,aspx -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster.txt
```
![[Pasted image 20240702123649.png]]

On Port 80
![[Pasted image 20240702122904.png]]

On http://10.10.10.88/robots.txt
![[Pasted image 20240702123015.png]]

Got some http://10.10.10.88/webservices/monstra-3.0.4/
![[Pasted image 20240702123205.png]]
Only this /webservices/monstra-3.0.4/ worked, all other entries doesn't exist

Will try to login, I have Just clicked the **logged in**  
![[Pasted image 20240702124427.png]]

It get me to admin login panel http://10.10.10.88/webservices/monstra-3.0.4/admin/index.php?id=dashboard
![[Pasted image 20240702124509.png]]

Also I have searched public exploit for Monstra 3.0.4, there are some RCE exploit for monstra, but all exploits are authenticated, so we need password to do successful exploit 

Will try to login with default password like admin:admin
It worked...!
![[Pasted image 20240702124730.png]]

After a lot of try, I can't progress, when i ran feroxbuster I can see wp 
![[Pasted image 20240702130751.png]]

On http://10.10.10.88/webservices/wp/
![[Pasted image 20240702130815.png]]

So will run wpscan
```
wpscan --url http://10.10.10.88:80/webservices/wp -e ap --plugins-detection aggressive
```
This will take an hour
![[Pasted image 20240702143428.png]]

Found gwolle-gb has running vulnerable version
![[Pasted image 20240702133729.png]]



Will Jump to Initial Foothold...!



I found a github repo, the user automated this process, We have to keep wp-load.php(our reverse shell script) hosted in the python web server , Link:https://github.com/igruntplay/exploit-CVE-2015-8351/blob/main/exploit.py
![[Pasted image 20240702135324.png]]

It has been taken
![[Pasted image 20240702135346.png]]

Got Reverse Shell
![[Pasted image 20240702135414.png]]

Let's Check Our Privilege
```
sudo -l
```
![[Pasted image 20240702135716.png]]

But we can't run this as www-data, we have to get access to Onuma.

I have used this command from GTFO bin, with small modification
```
sudo -u onuma tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
```
![[Pasted image 20240702140942.png]]
I got Shell as Onuma

Will get user.txt
![[Pasted image 20240702141046.png]]

Will Jump to Privilege Escalation.....!




I have just Done it using PwnKit, Because the system is running vulnerable version of Pkexec 0.105
![[Pasted image 20240702142417.png]]

Got root.txt
![[Pasted image 20240702142447.png]]