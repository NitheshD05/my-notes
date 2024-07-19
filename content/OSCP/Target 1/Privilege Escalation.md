Found interesting files in smith's home directory
![[Pasted image 20240605190740.png]]
went to read script.py and /tmp/log.crypt
![[Pasted image 20240605192020.png]]

base 64 decoded conetent:
![[Pasted image 20240605192122.png]]
![[Pasted image 20240605192159.png]]

found id_rsa
![[Pasted image 20240605190840.png]]

then tried ssh login with id_rsa:
![[Pasted image 20240605191255.png]]
updated shell to interactive
![[Pasted image 20240605191336.png]]

found suid's
![[Pasted image 20240605191441.png]]

Downloaded PwnKit
![[Pasted image 20240605191709.png]]
but didn't work:
![[Pasted image 20240605191749.png]]
got linpeas:
![[Pasted image 20240605191812.png]]
![[Pasted image 20240605191825.png]]
linpeas indicates the PE vector, but we dont have write access on script.py 
![[Pasted image 20240605192706.png]]

tried dirt pipe: https://github.com/AlexisAhmed/CVE-2022-0847-DirtyPipe-Exploits/blob/main/README.md
![[Pasted image 20240605194200.png]]

![[Pasted image 20240605194353.png]]
![[Pasted image 20240605194333.png]]

ran pspy
![[Pasted image 20240605194549.png]]
![[Pasted image 20240605194606.png]]

possible PE vector:
![[Pasted image 20240605194806.png]]
![[Pasted image 20240605195124.png]]

aging checked for pkexec 
![[Pasted image 20240605201350.png]]

search exploit for the version and found:https://packetstormsecurity.com/files/165739/PolicyKit-1-0.105-31-Privilege-Escalation.html

![[Pasted image 20240605201517.png]]
![[Pasted image 20240605201533.png]]


And tried the exploit
![[Pasted image 20240605201906.png]]
![[Pasted image 20240605201929.png]]
didn't work either
