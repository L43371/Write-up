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
