# Gotta Catch'em All! is an easy difficulty level created by Hifumi1337.

## Task 1

Find the Grass-Type Pokemon

Find the Water-Type Pokemon

Find the Fire-Type Pokemon

Who is Root's Favorite Pokemon?

First, we will start with Nmap tool.

Command:

`nmap -sV -sC -p- --open MACHINE_IP`

![image](https://github.com/L43371/THM-Gotta-Catch-em-All--Write-up/assets/129752764/ca71ed12-5bfd-41f0-8439-33e19946a4bd)

We got 2 ports.

Port 22 - SSH

Port 80 - HTTP

Checking the site and going to the page source, we see an interesting note.

![image](https://github.com/L43371/THM-Gotta-Catch-em-All--Write-up/assets/129752764/aaf99028-e8fb-4ef4-9780-7abacb80c027)

Checking the console, we see a list of pokemons.

![image](https://github.com/L43371/THM-Gotta-Catch-em-All--Write-up/assets/129752764/3cb5b58f-2665-4b0f-923b-1cba645f248b)

We will create a text file with this list, to be use later.

We will now check for directories, we will use the tool Gobuster.

Command:

`gobuster dir -u MACHINE_IP -w /usr/share/wordlists/dirb/common.txt`

We do not see any useful directory, after using Burpsuite, there is still nothing that we can use.

I then went back to check the page source again, and noticed this info and tried it to use to SSH to the machine.

![image](https://github.com/L43371/THM-Gotta-Catch-em-All--Write-up/assets/129752764/1ff79504-6a2f-479a-8849-b1805571383d)

and it works.

Going to the desktop, there is a zip file and I opened a http server to the machine that we are connected to and on our machine, we did a `wget` command to download the zip file.

and on my machine, I unzip the file using command `unzip -e 'file'` and it contains a text file, opening it, it is in hex, we will use a tool called cyberchef to convert it to a text that we can understand.

![image](https://github.com/L43371/THM-Gotta-Catch-em-All--Write-up/assets/129752764/01da0b55-57e1-4f29-a8aa-53c799b55a84)

And we got the answer on the first question

![image](https://github.com/L43371/THM-Gotta-Catch-em-All--Write-up/assets/129752764/13783fb2-3fca-45a8-9900-1cd2e44b9c96)

Navigating more and we see another folder in the Videos and going to the bottom, we see txt file that it looks like acredential.

![image](https://github.com/L43371/THM-Gotta-Catch-em-All--Write-up/assets/129752764/7727ea18-826a-4b34-ac2d-64662a0973d2)

Opening a new SSH session and use the credential and it works but we do not have access to /home/ash.

![image](https://github.com/L43371/THM-Gotta-Catch-em-All--Write-up/assets/129752764/87b5542c-27ed-4c0c-a433-04995d77401c)

Doing a find command, we can search for the answer for Q2

![image](https://github.com/L43371/THM-Gotta-Catch-em-All--Write-up/assets/129752764/ff32e56e-5a34-4bc2-90e7-9e3cdad6b025)

Using https://cryptii.com/pipes/hex-decoder 

Doing a find command, we can search for the answer for Q3

![image](https://github.com/L43371/THM-Gotta-Catch-em-All--Write-up/assets/129752764/0cea0f35-198a-4923-bb5e-3ce9076d571e)

Using https://www.base64decode.org/, we can decode it.

Going back to /home and there is a file that we can open.

![image](https://github.com/L43371/THM-Gotta-Catch-em-All--Write-up/assets/129752764/c3ca6087-3fd4-4121-87ed-0314cbd853c5)

We got the answer for the 4th question.


