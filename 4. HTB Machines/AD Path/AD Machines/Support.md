# 1. Nmap: 
-> I always want to start here to see what ports are open and what services are running.

Lets start with a basic scan and see what we can find:
`sudo nmap -sV -sC 10.10.11.51 -oN nmap.basic`

When that scan is done I want to do a deeper scan and run that while I do other tasks.

`sudo nmap -p- -sV -sC --script=default,vuln,exploit -T4 -oN nmap.full 10.10.11.51`

==Try to find valid users with nmap==: 
`sudo nmap -p 88 --script=krb5-enum-users --script-args krb5-enum-users.realm='sequel.htb',userdb=/usr/share/wordlists/statistically-likely-usernames/us-census-base-lists/j.smith-x98800.txt 10.10.11.51`

==Domain==: support.htb0


`smbclient -L \\10.129.95.187\\ -N`
![[Pasted image 20250803142941.png]]
### Login to share:
`smbclient \\\\10.10.11.174\\support-tools`

