# Year of the Rabbit is an Easy difficulty room made by MuirlandOracle.
We have 2 tasks, first, we need to retrieve the user flag and second, the root flag.

**TASK 1**

What is the user flag?

First step is to run Nmap command.

The command will be:

`nmap -sV -sC -p- --open $IP`

Result:

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/651f59e5-3d32-43e3-af3e-421b5e1143ee)

We see on the result that there are 3 open ports:

Port 21 - FTP

Port 22 - SSH

Port 80 - HTTP

There are some cases that anonymous login is allowed to login to FTP, we will try it here

The command will be:

`ftp $IP`

Result:

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/6c9dfea5-9b6d-4959-b5d6-62407440764e)

It seems anonymous login is not allowed.

We will now check the website

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/6389c90f-4996-4c0a-a6da-3aa18378dd55)

Looks like it is just a default page.

We will now use `feroxbuster` command to check any directories

The command will be:

`feroxbuster -u http://10.10.151.28/ -t 200 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/4590491f-2684-46a4-8e02-236335b60c59)

We can see there is a directory called assets.

Going to the assets page, we can see there are 2 files

RickRolled.mp4 and style.css

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/1f9f767f-cba8-496b-8d84-f06c0f017e78)

When checking the style.css we can see that there is a line in there to take a look at the page: /sup3r_s3cr3t_fl4g.php

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/9dcca361-523f-47f0-a051-fe8131f717e3)

We will now check the page that was indicated

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/318f96f6-fba1-4537-a53a-7d9097f1a4f3)

We got this popup to turn off java script and when pressing OK, it will redirect to a YouTube video.

To disable javascript, on your browser, in my case FireFox, type `about:config` in the URL bar.

Search for `javascript.enabled` and change it to false.

Now going back to the previous page, we can see a different result

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/b06c9e3a-7f09-45aa-9e49-57ac2817a5af)

Now on this page, I went and check the source code, inspect, but I do not see anything in there that will help me. 

We will now use BurpSuite to intercept the page and see if there is something unusual.

By intercepting the page and sending it to Repeater, we can see that there is another hidden directory

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/7ee0c0de-e379-4a3b-84f7-02a3fe2a2b37)

Going to that directory we can now see a file named Hot_Babe.png

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/dfc2443f-a214-4862-8b15-0997a23e0e4b)

We will download the file and check if there are some hidden information on it.

Now, we will use a command called binwalk.

The command will be:

`binwalk Hot_Babe.png`

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/df09e635-84ca-41f6-9a21-260f50adf966)

We can see that there is a Zlib compressed data, now to extract this, we will issue -e command inside the binwalk

The command will be:

`binwalk Hot-Babe.png -e`

It will create a new directory

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/38a11a99-3476-4201-8eb2-40a1632e00b1)

Going inside the created directory, we can see 2 files.

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/05914016-bea5-4faa-8606-488fd883124c)

Doing `cat 36.zlib` we can see a wall of text but at the bottom, there are some useful information.

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/eef92e40-fa9a-46a0-aeb3-f51873e61348)

We have now an FTP user that we can use _ftpuser_ and copy the password list and create a txt file.

Now, we will brute force the password of _ftpuser_, the tool that we will be using is called `hydra`.

The command will be:

`hydra -f -V -l ftpuser -P password.txt ftp://IPaddress`

We found the password!

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/543f4d19-8194-4224-b84d-6c0dec432e80)

Now we will try and FTP using the username and password that we got.

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/ef6fb583-48b0-49fc-aca4-01c06cbac086)

and we are in!

We can see that there is a txt file, we will use the get command to download the file to our machine.

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/e2445329-724d-4339-a169-a2c6063194b3)

opening the txt file that we download, it looks like it is some sort of encryption, checking google we can use this site https://www.dcode.fr/brainfuck-language to decrypt the txt file.

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/410a0fa6-17cb-440e-bc68-d984196182f3)

We got another credential

We will now try to SSH using this credential.

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/b36ef39d-3662-4a91-a83c-9c2587ec956a)

We are in and we see a message.

We will now try to find the first flag, to make things easier you can do a find command.

The command will be:

`find / -type f -name user.txt 2>/dev/null`

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/3aae0f2f-5e42-4ec1-a923-1d2558a6c86f)

But it looks like we do not have permission to open it.

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/77c493e5-2610-42f2-accf-2219f0e2d7c7)

We will now try to find that leet s3cr3t hiding place that was mentioned in the message.

We will use the find command again.

`find / -type d -name s3cr3t 2>/dev/null`

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/5ead4ade-81ec-4960-9848-9e67f3b80117)

Going to the directory and issuing a `ls -lah` command, we can see that there is one file

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/9561091e-a754-4381-97e6-522f6483adac)

Opening the file, we can see our third credential

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/26bdf95a-f72d-40f2-bd52-ec8a30e605eb)

Let's open a new Terminal tab and ssh using our third credential

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/9173d4cd-06f6-45a2-90a3-59ee1e2674e6)

and it works, now we can retrieve the first flag.

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/742e7164-4c7c-475d-81dc-f81f1a16bb64)

and now we need to find the root flag, and so we need to escalate our privilege.

**TASK 2**

What is the root flag?

By doing `sudo -l` command we can see that the user can edit the txt file with sudo privileges using the /usr/bin/vi command but the issue is we cannot run sudo with root as we have (ALL.!root) here.

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/afe736bf-f4fe-4acc-81f9-eade42da7680)

By doing research, there is a way to get a privilege, there is a [CVE-2019-14287](https://www.exploit-db.com/exploits/47502) that we can use.

We can use the command:

`sudo -u#-1 /usr/bin/vi /home/gwendoline/user.txt`

This will open the vi editor and inside, by pressing **Shift** and **:** button and then type `!/bin/sh` and hitting enter

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/3044403c-c7b8-45d3-8200-45cb01b3a56e)

We are now root!

Then we can now retrieve the second flag

![image](https://github.com/L43371/Year-of-the-rabbit-THM-writeup/assets/129752764/94dd7657-7c42-4609-8a65-fbe8c601e37a)
