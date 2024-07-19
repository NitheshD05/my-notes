**Initial Nmap Scan:** Found quit a lot open ports in the initial scan
```
nmap 192.168.191.141 -oN dnmap
```
![[Pasted image 20240602124510.png]]
Now will find the service running on each port and enumerate accordingly

**Script and Service scan:** We found all most all services
```
nmap 192.168.191.141 -sC -sV -oN snmap 
```
![[Pasted image 20240602124951.png]]
![[Pasted image 20240602125130.png]]

Now will enumerate one by one

**Port 80:** It a web page on port 80
![[Pasted image 20240602125425.png]]

**Port 81:** On port 81 there is a attendance and payroll site
![[Pasted image 20240602125536.png]]

Lets run the Gobuster on both http ports : 
```
gobuster dir -u http://192.168.191.141:81/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,conf -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster.txt

gobuster dir -u http://192.168.191.141/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,conf -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster80.txt
```
![[Pasted image 20240602133728.png]]
Can see a lot of 200 ok directories in Port 81.

![[Pasted image 20240602144429.png]]
Can see lot of 200 ok directories in port 80 as well, lets enumerate it later.

**Now lets enumerate SMB ports 139, 445:**
```
smbclient -U '' -L \\\\192.168.191.141\\ 
smbmap -H 192.168.191.141
smbclient -L \\\\192.168.191.141\\
```

![[Pasted image 20240602143803.png]]
Nothing works, will move on

**Now lets enumerate mysql port 3306:**
```
mysql -u root -h 192.168.191.141 -p
```

![[Pasted image 20240602144119.png]]
We are not allowed to login from our kali machine


As of now we are done with initial enumeration, will move to get Initial Foothold with the findings

If it not work, will come back again and enumerate the services and ports further.