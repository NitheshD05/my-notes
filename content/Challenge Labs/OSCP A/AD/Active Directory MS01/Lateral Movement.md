**Lets see our privileges:**
```
whoami /priv
```
![[Pasted image 20240602145003.png]]
so we have quite Interesting privileges like SeImpersonate

First will change to powershell, only because i like to work on powershell, thats it.

**System Informations:**
```
systeminfo
```
![[Pasted image 20240602145631.png]]

**Users in the computer**:
```
ls
net user
```
![[Pasted image 20240602145850.png]]

**Groups:**
```
whoami /groups
```

![[Pasted image 20240602151159.png]]

**C Directory:**
```
ls
```
![[Pasted image 20240602151331.png]]

As of now we get to Know that we have some Interesting Privileges, will try to escalate our Privilege using that.

If it not works, will come back here and dig further and further.

Now will move to Privilege Escalation....!