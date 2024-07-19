#### **PSQL Enumeration**

Psql Connect.
```
psql -U postgres -p 5432 -h 192.168.xxx.xxx
```
Try to login with default credentials.

Psql Command.
```
\l -- List all databases 
\c <database_name> -- Connect to a specific database 
\dt -- List all tables in the current database 
SELECT * FROM <table_name>; -- Retrieve data from a table
```

PSQL Brute Forcing using Hydra
```
hydra -L /usr/share/metasploit-framework/data/wordlists/postgres_default_user.txt -P /usr/share/metasploit-framework/data/wordlists/postgres_default_pass.txt IP postgres
```
