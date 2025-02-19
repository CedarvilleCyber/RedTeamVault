IP: 10.10.11.35

Nmap scan: `sudo nmap -sC -sV -T4 -oN versions.nmap 10.10.11.35`

## First steps
- Kerberos (88)
- SMB (445)
- LDAP (389 (default) 636 (unencrypted))

## Initial SMB notes
- Vulnerable to SMBghost module in Metasploit (search smb 3.1.1)

## Kerberos notes
- Lamoreaux used Kerbrute to get valid usernames.
- `kerbrute userenum -d cicada.htb --dc 10.10.11.35 /usr/share/wordlists/SecLists/SecLists/Usernames/xato-net-10-million-usernames-dup.txt`
	- ADMINISTRATOR@cicada.htb
	- Administrator@cicada.htb
	- administrator@cicada.htb
	- GUEST@cicada.htb
	- Guest@cicada.htb
	- guest@cicada.htb

## Further SMB notes
Netexec:
![[Cicada-SMB-shares.png]]

`smbclient //10.10.11.35/HR -N`
![[Cicada-SMB-HR.png]]

Default password from the HR SMB share: 
>Cicada$M6Corpb*@Lp#nZp!8
- Now we're trying to find the username.

Use `impacket lookupsids` to find the username. 