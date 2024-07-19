#### **LDAP Enumeration**

Using ldapsearch, change domain accordingly.
```
ldapsearch -v -x -b "DC=hutch,DC=offsec" -H "ldap://192.168.189.122" "(objectclass=*)"
```

If you have credentials, try this command.
```
cadaver http://192.168.xxx.xxx
```
