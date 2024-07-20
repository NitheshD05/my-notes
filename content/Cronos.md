![[Pasted image 20240720104939.png]]

**Ping**
```
ping 10.10.10.13
```
![[Pasted image 20240619163028.png]]

Nmap Default Scan 
```
nmap 10.10.10.13 -oN dnmap
```
![[Pasted image 20240619163118.png]]

Nmap Script and Service Scan
```
nmap 10.10.10.13 -sV -sC -A -oN snmap
```
![[Pasted image 20240619163313.png]]
![[Pasted image 20240619163337.png]]

Nmap All port Scan
```
nmap -p- --min-rate 10000 10.10.10.13 -oN anmap
```
![[Pasted image 20240619163253.png]]

Port 80
http://10.10.10.13
![[Pasted image 20240619164433.png]]

http://cronos.htb
![[Pasted image 20240619164359.png]]

Will dig Further
```
dig axfr cronos.htb @10.10.10.13
```
![[Pasted image 20240619170959.png]]
got additional dns info, will add it to hosts file

http://admin.cronos.htb
![[Pasted image 20240619171929.png]]
used simple sql injection

It worked....!
![[Pasted image 20240619171955.png]]

We can ping as well
![[Pasted image 20240619172052.png]]

Tried my ip
![[Pasted image 20240619172130.png]]

Will get the burp request
![[Pasted image 20240619172259.png]]
looks like we can execute commands

Will try that
![[Pasted image 20240619170658.png]]
yes it worked, we can see the python in the machine

Now will get reverse shel
![[Pasted image 20240619170902.png]]

Got Reverse Shell and upgraded my shell
```
rlwrap nc -nvlp 443
```
```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```
![[Pasted image 20240619172722.png]]

Got user.txt 
![[Pasted image 20240619172837.png]]

Will Move to Privilege Escalation....!

We can see db creds in config file.
![[Pasted image 20240619172938.png]]
After through Enumeration on the mysql, there is nothing useful

Will check is there a cronjob
```
cat /etc/crontab
```
![[Pasted image 20240619174255.png]]

Will check our permissions on the file running by root
```
ls -la /var/www/laravel/artisan
```
![[Pasted image 20240619174342.png]]

We have full accesss, so we can modify the php code into out reverse shell Program
![[Pasted image 20240619174451.png]]
I have used this php reverse shell code: https://github.com/ivan-sincek/php-reverse-shell/blob/master/src/reverse/php_reverse_shell.php

And set up the listener
```
rlwrap nc -nvlp 443
```
![[Pasted image 20240619174715.png]]Got it....!

**Root.txt**
![[Pasted image 20240619174804.png]]



Done with Editorial....:)