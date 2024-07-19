#### **Initial Discovery:**
**Initial Scan.**
```
nmap ip -sS -oN dnmap
```
**All port Scan.**
```
nmap ip -p- --open -T5 -oN anamp
```
**Script, Service and OS Scan.**
```
nmap ip -sV -sC -A -oN snmap
```
**Full scan.**
```
nmap ip -sV -sS -sC -A -T4 -oN fnamp
```
```
nmap -p- --min-rate 10000 ip -oN inmap
```
**Detailed Scan.**
```
nmap ip -sV -sS -sC -A -T4 -p- --open -oN Dnamp
```
**UDP Scan.**
```
nmap ip -p 161 -oN udp_scan
```
```
nmap ip -p 53,67,68,69,123,161,162,137,138,139,445,500,514,520,1194,1900,4500,5353,623 -oN udp_fullscan
```

Always Try this
```
nmap -sT -p- --min-rate 10000 ip
```
#### **Script Scan:**

**VULN Scan**
```
--script vuln
```

**Smb Scripts:**
```
--script smb-vuln*
```
```
--script smb-enum-shares.nse
```
```
--script smb-enum-users.nse
```

**Ldap Scripts:**
```
--script "ldap* and not brute" 
```
```
--script ldap-brute --script-args ldap.base='"cn=users,dc=cqure,dc=net"'
```
```
--script ldap-rootdse
```
```
--script ldap-search --script-args 'ldap.username="cn=ldaptest,cn=users,dc=cqure,dc=net",ldap.password=ldaptest,ldap.qfilter=users,ldap.attrib=sAMAccountName'
```
```
--script ldap-search --script-args 'ldap.username="cn=ldaptest,cn=users,dc=cqure,dc=net",ldap.password=ldaptest,ldap.qfilter=custom,ldap.searchattrib="operatingSystem",ldap.searchvalue="Windows *Server*",ldap.attrib={operatingSystem,whencreated,OperatingSystemServicePack}'
```

**Snmp Script:**
```
--script *snmp*
```

**Kerb Port 88:**
```
--script krb5-enum-users --script-args krb5-enum-users.realm='test'
```
