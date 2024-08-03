#### **MYSQL Enumeration**

Mysql Connect.
```
mysql -u username -p password
```

Try Login without Password.
```
mysql -u root -p
```

Try with Password.
```
mysql -u username -ppassword
```

Mysql Commands.
```
SHOW DATABASES; 
USE <database_name>; 
SHOW TABLES; 
SELECT * FROM <table_name>; 
SHOW GRANTS;
INSERT INTO users (name, age) VALUES ('John', 30);
DELETE FROM users WHERE name = 'John';
COMMIT;  \\use this for commit chnages, only if you are not in auto commit mode. 
```

Mysql database access without a mysql shell
```
mysql --database databasename -e 'show databases;' -u username -p
```

Grep data from table
```
mysql --database capiclean -e 'use database_name; show tables; select * from table_name' -u username -p
```

Get Table info from database
```
mysql --database capiclean -e 'use capiclean; show tables;' -u iclean -p
```

Check who can impersonate as sysadmin
```
SELECT distinct b.name FROM sys.server_permissions a INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id WHERE a.permission_name = 'IMPERSONATE'
```