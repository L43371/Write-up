Start with Nmap scan

Command

`IP=10.10.11.233;\
> ports=$(nmap -p- --min-rate=1000 -n -T4 $IP | grep ^[0-9] | cut -d '/' -f 1 | tr '\n' ',' | sed s/,$//);\
> nmap -Pn -sC -sV -p$ports $IP -oN scan.txt --reason --script=vuln`
> 
