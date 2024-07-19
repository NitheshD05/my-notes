
Windows Powershell command to find file in entire system.
```
Get-ChildItem -Path C:\ -Filter "fILENAME" -Recurse -ErrorAction SilentlyContinue
```

Windows cmd Command to find file in entire system.
```
dir /s /b C:\flag.txt
```
```
dir /S C:\Path\To\Directory\*.kdbx *.log *.ini *.txt *.conf
```
Windows Command to find a files with extensions. 
```
Get-ChildItem -Path C:\Users -Include *.txt, *.log, *.kdbx, *.conf -File -Recurse -ErrorAction SilentlyContinue
```

Download from websites and from other machines Using Download Function.
```
powershell.exe -c "(New-Object System.Net.WebClient).DownloadFile('http://example.com/file.txt', 'C:\path\to\save\file.txt')"
```
```
powershell -c "(New-Object System.Net.WebClient).DownloadFile('http://example.com/file.txt', 'C:\path\to\save\file.txt')"
```

RUNAS command for windows Powershell.
```
runas /user:USER_NAME cmd
```



