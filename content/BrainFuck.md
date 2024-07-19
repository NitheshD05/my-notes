**Ping Scan**
We can reach the machine..!
```
ping 10.10.10.17
```
![[Pasted image 20240610214951.png]]

**Nmap default scan**
```
nmap 10.10.10.17 -oN dnmap
```
![[Pasted image 20240610215138.png]]

**Script And Service Scan**
```
nmap 10.10.10.17 -sV -sC -oN snmap
```
![[Pasted image 20240610215228.png]]
![[Pasted image 20240610215244.png]]

**Nmap All Port Scan**
```
nmap 10.10.10.17 -p- --open -T5 -sV -sC -oN anmap
```

No new port identified.

After enumerating port, I landed at dead end, so the tried to add the DNS name of the machine to my host file and checked.
![[Pasted image 20240611000635.png]]

My hosts file
![[Pasted image 20240611000713.png]]


We have been redirected to https://brainfuck.htb/ while tried to reach https://www.brainfuck.htb on browser.
![[Pasted image 20240611000733.png]]

And thew other site https://sup3rs3cr3t.brainfuck.htb/
![[Pasted image 20240611000946.png]]

Will go with the first site https://brainfuck.htb/ We can see it is a wordpress site, So will use wpscan for any vulnerable plugins
![[Pasted image 20240611001128.png]]

```
wpscan --url https://brainfuck.htb --disable-tls-checks
```
![[Pasted image 20240611001215.png]]
Found a vulnerable plugin.


Lets search exploit for a outdated plugin we have found using Wpscan 
![[Pasted image 20240611001335.png]]
And there is…! https://www.exploit-db.com/exploits/41006

We have PoC here.
![[Pasted image 20240611001551.png]]


Will try the exploit
Sooo, first he have to create a post request form
![[Pasted image 20240611002701.png]]

And host a python web Server on the same directory
```
python3 -m http.server  80
```
![[Pasted image 20240611002739.png]]

And i have opened the test.html
![[Pasted image 20240611002857.png]]

![[Pasted image 20240611002932.png]]
I  can see the simple form, it is initial username is administrator, I have changed it to admin because administrator doesn't worked.

Now will just press login and will get a empty page
![[Pasted image 20240611003206.png]]
It has loaded the cookies accordingly.

Just reload the original page , Now we are admin
![[Pasted image 20240611003128.png]]

Then get into wordpress dashboard, can't edit themes or add vulnerable plugin to get a web or reverse shell.

But while inspecting the smpt plugin, I can see the potential creds if User 'orestis' 
![[Pasted image 20240611004453.png]]
![[Pasted image 20240611004031.png]]
So the creds (orestis:kHGuERB29DNiNE) for SMTP

Now we have SMTP creds, so will use Evolution, a mail application.
![[Pasted image 20240611005422.png]]

Set up the mailbox with  creds and out ser er details.
![[Pasted image 20240611005454.png]]
![[Pasted image 20240611005501.png]]

And we found another creds, this for the other site https://sup3rs3cr3t.brainfuck.htb/ I guess 
![[Pasted image 20240611005607.png]]

Yep, its correct,  I can login as user orestis
![[Pasted image 20240611005715.png]]
And Found more threads.

Everything is encrypted here, will try to decrypt this
![[Pasted image 20240611005752.png]]
We can see the message from orestis end with same structure, so it might be "Orestis - Hacking for fun and profit"
So will use this encrypted value and its original value to find a key pass

Tried using python
```
enc = "Pieagnm - Jkoijeg nbw zwx mle grwsnn"
pt = "Orestis - Hacking for fun and profit"
assert len(enc) == len(pt)
list(zip(enc, pt))
[ord(e)-ord(p) for e,p in zip(enc, pt)]
[(ord(e)-ord(p))%26 for e,p in zip(enc, pt)]
[(ord(e)-ord(p))%26 + ord('a') for e,p in zip(enc, pt)]
[chr((ord(e)-ord(p))%26 + ord('a')) for e,p in zip(enc, pt)]
```
![[Pasted image 20240611010819.png]]
So it should be FUCKMYBRAIN or BRAINFUCKBARIN, MYBARINFUCK

Cool, FUCKMYBRAIN worked
![[Pasted image 20240611011723.png]]

Then we found the RSA location
![[Pasted image 20240611011829.png]]

Got it...!
![[Pasted image 20240611011913.png]]

The id_rsa is password Protected, So will use John to find the Passphrase
![[Pasted image 20240611012359.png]]

Using john to find Passphrase  
```
ssh2john id_rsa > rsa.hash
```
```
john --wordlist=/usr/share/wordlists/rockyou.txt rsa.hash
```
![[Pasted image 20240611012437.png]]
We found the Passphrase '3poulakia!'

Now will try to get ssh into the Machine, before that change permission for id_rsa
```
chmod 600 id_rsa
```
```
ssh orestis@10.10.10.17 -i id_rsa
```
![[Pasted image 20240611012646.png]]
We are in......!

Found the user.txt in home directory of orestis
![[Pasted image 20240611012833.png]]



We can also see some other files in home directory
![[Pasted image 20240611013309.png]]
debug.txt, encrypt.sage, mail and output.txt
Output.txt ahs the encrypted password
The encrypt.sage is python script, its look like RSA key generation 

There is a code in stack overflow to decrypt the value encrypted value: https://crypto.stackexchange.com/questions/19444/rsa-given-q-p-and-e
![[Pasted image 20240611013648.png]]

Using that code to decrypt the password and changed the value of p, q, e, and ct accordingly
![[Pasted image 20240611013916.png]]

Then Ran it.
![[Pasted image 20240611013959.png]]
We got pt and n, the script provided the plaintext as a large integer, so will convert it to ASCII using python
```
pt = 24604052029401386049980296953784287079059245867880966944246662849341507003750
f"{pt:x}"
bytes.fromhex(f"{pt:x}").decode()
```
![[Pasted image 20240611014445.png]]
So we found root.txt, but still have to get root access.

We can see pkexec have SUID
![[Pasted image 20240611015126.png]]

And it is running on vulnerable version
![[Pasted image 20240611015310.png]]

Will try PwnKit to escalate our privileges
![[Pasted image 20240611015558.png]]
Transferred PwnKit to target machine using wget and the python server is hosted on my kali machine where the PwnKit is located. 

Will change the permissions and run to elevate our privileges
![[Pasted image 20240611015640.png]]

We can find our root.txt file in root's home directory
![[Pasted image 20240611015716.png]]


This is really BrAiNinFuCk 
Finally Done.....:)
