#### **SNMP Enumeration**

**Using SNMPWalk For Windows Machine.**
```
snmpwalk -c public -v1 -t 10 192.168.243.145  \\Using snmpwalk to enumerate the entire MIB tree

snmpwalk -c public -v1 192.168.243.145 1.3.6.1.4.1.77.1.2.25  \\Using snmpwalk to enumerate Windows users

snmpwalk -c public -v1 192.168.243.145 1.3.6.1.2.1.25.4.2.1.2   \\Using snmpwalk to enumerate Windows processes

snmpwalk -c public -v1 192.168.243.145 1.3.6.1.2.1.25.6.3.1.2   \\Using snmpwalk to enumerate installed software

snmpwalk -c public -v1 192.168.243.145 1.3.6.1.2.1.6.13.1.3   \\Using snmpwalk to enumerate open TCP ports
```

**For Linux Machine.**
```
snmpwalk -v2c -c public 192.168.236.149 NET-SNssh -N -L 8000:localhost:8000 -p 2222 dev@192.168.236.150MP-EXTEND-MIB::nsExtendObjects  \\change ip, and look for version and change accordingly

snmpwalk -c public -v1 192.168.236.149 .1.3.6.1.2.1.1     # Enumerate system information

snmpwalk -c public -v1 192.168.236.149 .1.3.6.1.2.1.2     # Enumerate network interfaces

snmpwalk -c public -v1 192.168.236.149 .1.3.6.1.2.1.25.4.2.1.2  # Enumerate running processes

snmpwalk -c public -v1 192.168.236.149 .1.3.6.1.2.1.25.6.3.1.2  # Enumerate installed software/packages

snmpwalk -c public -v1 192.168.236.149 .1.3.6.1.2.1.25.2   # Enumerate filesystem information

snmpwalk -c public -v1 192.168.236.149 .1.3.6.1.2.1.25.4   # Enumerate running services

snmpwalk -c public -v1 192.168.236.149 .1.3.6.1.2.1.25.1   # Enumerate system users
```