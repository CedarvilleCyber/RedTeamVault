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