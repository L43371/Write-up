# Brute It is an Easy difficulty level made by ReddyyZ

## Task 1

Deploy the machine

## Task 2 

Q1: How many ports are open?

Q2: What version of SSH is running?

Q3: What version of Apache is running?

Q4: Which Linux distribution is running?

Q5: Search for hidden directories on web server. What is the hidden directory?

First, We will use `Nmap` command.

Command:

`nmap -sV -sC -p- --open Machine_IP` 

![image](https://github.com/L43371/THM---Brute-It-Write-up/assets/129752764/0b9e117d-8a0f-48a0-89b4-139f4e729f42)

We can get the answer from this result for Q1-Q4.

Second, we will use Feroxbuster to gather the directories.

Command:

`feroxbuster -u http://Machine_IP/ -t 200 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`

The feroxbuster result gave us 2 directories

![image](https://github.com/L43371/THM---Brute-It-Write-up/assets/129752764/3ff0cd94-9023-4e40-a797-f5479a8fb282)

Which is the answer for Q5.

## Task 3 

Q1: what is the user:password of the admin panel?

Q2: Crack the RSA key you found. What is John's RSA Private Key passphrase?

Q3: user.txt

Q4: Web flag

Going to the /admin directory, we are greeted by a login page, I tried the combination of admin:admin, admin:password, admin:pass, but it doesn't work.

I then view the page source, and there is a note there.

![image](https://github.com/L43371/THM---Brute-It-Write-up/assets/129752764/f022559d-107d-4039-a41d-68b48f55c927)

We now know that the username **_admin_** is valid.

We will be using **_hydra_** to brute force the password

Command:

`hydra -l admin -P /usr/share/wordlists/rockyou.txt Machine_IP http-post-form "/admin/index.php:user=^USER^&pass=^PASS^:Username or password invalid"`

We are sucessful at brute forcing the password.

![image](https://github.com/L43371/THM---Brute-It-Write-up/assets/129752764/179bc4fb-d992-4043-94ea-5fdd4ffb1adc)

Logging in using the credential that we got, we can see the web flag and a RSA private key.

![image](https://github.com/L43371/THM---Brute-It-Write-up/assets/129752764/d11e3a6d-f20d-427d-85d7-6e88289fab66)

We will copy or download the RSA private key.

Now, we need to change the access permission of the file that we downloaded.

Command:

`chmod 600 id_rsa`

We will now try to ssh using the user _john_ and use the RSA file that we got.

Command:

`ssh john@Machine_IP -i id_rsa `

![image](https://github.com/L43371/THM---Brute-It-Write-up/assets/129752764/ab0f3bfd-eea9-41cb-95a9-790db4a70c9e)

But it seems it needs a passphrase, we will use a tool called ssh2john.

Command:

`./ssh2john.py ~/id_rsa > hash.txt`

then we will use _john the ripper_ to crack the file that was created.

Command:

`john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt `

![image](https://github.com/L43371/THM---Brute-It-Write-up/assets/129752764/a7ee346b-922f-48b9-9302-5fc53f77fcee)

We got the passphrase!

By using the RSA and passphrase, we can now SSH using _john_ credential.

![image](https://github.com/L43371/THM---Brute-It-Write-up/assets/129752764/d3f19631-e72c-4727-9116-ac2797aeeb50)

Then inside we can retrieve the user flag. 

![image](https://github.com/L43371/THM---Brute-It-Write-up/assets/129752764/057bb80a-0562-4300-ad91-67e148d94a7b)

## Task 4 

Q1: Find a form to escalate your privileges. What is the root's password?

Q2: root.txt

We now need to find a way to escalate our access.

By doing the sudo -l command, we can see that the user can run sudo /bin/cat without the password.

![image](https://github.com/L43371/THM---Brute-It-Write-up/assets/129752764/0015bdd1-8cb7-47dc-ab87-903033a6f91e)

Because we can do sudo cat, we will try to do sudo cat /etc/shadow, and success we can see the password hash of root.

![image](https://github.com/L43371/THM---Brute-It-Write-up/assets/129752764/53fdee73-69ed-41ff-8b09-b7aadb391d36)

We will copy the hash and use hashcat to crack the password.

Command:

`hashcat -m 1800 -a 0 roothash.txt /usr/share/wordlists/rockyou.txt`

Success! We got the password.

![image](https://github.com/L43371/THM---Brute-It-Write-up/assets/129752764/27fe3336-74eb-4a46-81a0-7e68526ffaa1)

Now, we will try to login as root.

![image](https://github.com/L43371/THM---Brute-It-Write-up/assets/129752764/f92641c1-0e7d-469c-8139-64eaeb31fc9e)

It works!

We can now retrieve the last flag.

![image](https://github.com/L43371/THM---Brute-It-Write-up/assets/129752764/0c495d51-9100-4c37-82ae-377c674db7bd)







