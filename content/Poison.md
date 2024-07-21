<img src="Pasted image 20240720105802.png" alt="HTB Profile Image" class="htb-profile-img">

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
ping 10.10.10.84
```
![[Pasted image 20240611123734.png]]
Can ping the Machine.

Nmap Default Scan
```
nmap 10.10.10.84 -oN dnmap
```
![[Pasted image 20240611130205.png]]

Script and Service scan
```
nmap 10.10.10.84 -sV -sC -p22,80 -oN snmap
```
![[Pasted image 20240611130305.png]]

All port Scan
```
nmap 10.10.10.84 -p- --open -T5 -sV -sC -oN anmap
```
![[Pasted image 20240611131309.png]]

On Port 80
![[Pasted image 20240611132937.png]]
Looks like We have file read permission.





Lets check by reading /etc/passwd
![[Pasted image 20240611133026.png]]
Yes it worked..! We can see there is user named Charix.

First will read all the file listed on the site, while reading listfiles.php
![[Pasted image 20240611133204.png]]We can see there another file we can read, that is pwdbackup.txt

Will read that
![[Pasted image 20240611133424.png]]
Looks like we found base64 encoded password. 

After decding it to 13 time, we can get plain text password of charix
![[Pasted image 20240611133600.png]]

Now will try to ssh to the machine
![[Pasted image 20240611133711.png]]

can get user.txt file
![[Pasted image 20240611133955.png]]

After trying all privilege escalation methods, I landed on dead end. So I have listed the ports that are listening from Local host.

```
sockstat -4 -l
```
![[Pasted image 20240611143621.png]]
We can see there is vnc on 5901, 5801

We already have a secret file for the password, which we extracted from secret.zip
![[Pasted image 20240611143745.png]]

Now i want to get the loopback on port 5901 to my kali
```
ssh -L 5901:localhost:5901 charix@10.10.10.84
```
![[Pasted image 20240611143935.png]]
Now we can reachport 5901 from our kali machine

So I transferred the secret file to my kali machine and used vncviewer
```
vncviewer -passwd secret 127.0.0.1:5901
```
![[Pasted image 20240611144021.png]]

i got new session as root in vncviewer
![[Pasted image 20240611144101.png]]

I added my public key to authorized keys and ssh to the machine as root and got ssh session as root
```
cp /root/.ssh/id_rsa .
chmod 600 id_rsa
```
```
ssh root@10.10.10.84 -i id_rsa 
```
![[Pasted image 20240611144242.png]]
![[Pasted image 20240611144257.png]]

And finally got root.txt
![[Pasted image 20240611144320.png]]


We are done with Poison....:)