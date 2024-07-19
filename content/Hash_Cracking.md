##### **Hashcat**

Find Mode for hash.
```
hashcat --help | grep -i "HASH_TYPE"
```

**Best rule files:**
```
-r /usr/share/hashcat/rules/best64.rule
```
```
-r /usr/share/hashcat/rules/rockyou-30000.rule
```
```
-r /usr/share/hashcat/rules/OneRuleToRuleThemAll.rule
```
```
-r /usr/share/hashcat/rules/d3ad0ne.rule
```
```
-r /usr/share/hashcat/rules/best64.rule
```
```
-r /usr/share/hashcat/rules/T0XlC.rule
```

Hashcat command to crack Keepass Masters Password.
```
hashcat -m 13400 hash.file /usr/share/wordlists/rockyou.txt --force
```

Hashcat command to crack kerberoast.
```
hashcat -m 13100 --force -a 0 kerb.hash /usr/share/wordlists/rockyou.txt
```

Hashcat command to crack Asreproast.
```
hashcat -m 18200 hashes.asreproast /usr/share/wordlists/rockyou.txt --force
```

Hashcat command to crack NTLM.
```
hashcat -m 1000 ntlm.hsah /usr/share/wordlists/rockyou.txt --force
```

Hashcat command to crack NTLMV2.
```
hashcat -m 5600 ntlmv2.hash /usr/share/wordlists/rockyou.txt --force 
```
