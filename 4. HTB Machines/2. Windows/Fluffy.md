# Communal Attempt
initial scan: `sudo nmap <ip> -T4 -p- --open -oN init.nmap`

Creds: `j.fleischman / J0elTHEM4n1990!`

![[Pasted image 20250906141422.png]]

`smbclient -N -L \\<ip>`
![[Pasted image 20250906142009.png]]

![[Pasted image 20250906142547.png]]

We found a list of CVEs on the SMB server in the IT share. We then used exploit code 

`sudo responder -I tun0 -w -d -F` 
# Solo Attempt
Creds: `j.fleischman / J0elTHEM4n1990!`
IP: `10.10.11.69`

## Initial scan
```
sudo nmap 10.10.11.69 -T4 -oN init.nmap
```
Services revealed: 
- 53 | DNS
- 88 | Kerberos
- 139 | NetBIOS
- 389 | LDAP
- 445 | SMB
- 464/tcp  open  kpasswd5
- 593/tcp  open  http-rpc-epmap
- 636/tcp  open  ldapssl (LDAP over SSL)
- 3268/tcp open  globalcatLDAP
- 3269/tcp open  globalcatLDAPssl
- 5985/tcp open  wsman - WinRM

No RDP. Since we're starting with creds, that would be too easy :)
## Enumeration
### SMB
Let's try looking at the SMB shares. Log in with `smbclient`: 
```shell
smbclient -N -L \\10.10.11.69    # null login (-N), list shares (-L)

\> ls
```
- Unique share: IT
- All the default shares showed up and were either password-protected or empty. The IT share, however, is not a default share, so let's look into it. 

```
smbclient \\\\10.10.11.69\\IT -U 'j.fleischman'
\> exit
mkdir smb_IT_share
cd smb_IT_share
smbclient \\\\10.10.11.69\\IT -U 'j.fleischman'
\> mget *
```
- I created the `smb_IT_share` directory to keep myself organized. This puts all the stuff from the IT share into a folder that won't be used for anything else.
- Note: `mget *` does not recurse into subdirectories and download the contents - it only downloads files in the main directory. That's good enough for this situation, though. 

Using our creds, we were able to log into the IT share, and there's some interesting stuff in there. However, we went this way last time and weren't able to get into the box, so I'm going to enumerate a couple other services before proceeding. 

I think there's a good chance that this CVE is for privilege escalation, not initial access, so hopefully we'll be able to get in through another way, then use this CVE to get root access on the box. 
### LDAP
To enumerate `ldap`, we're going to use `nxc` (NetExec). Use https://netexec.wiki to learn about this incredible tool.

```
nxc ldap 10.10.11.69
```

Output: 
`LDAP        10.10.11.69     389    DC01             [*] Windows 10 / Server 2019 Build 17763 (name:DC01) (domain:fluffy.htb)`
- This reveals the LDAP service on the default port (389)

Now, let's try enumerating the users on the machine.
```
nxc ldap 10.10.11.69 -u 'j.fleischman' -p 'J0elTHEM4n1990!' --users
```

Result: 
![[Pasted image 20250908134043.png]]

Enumerating the groups: 
```
nxc ldap 10.10.11.69 -u 'j.fleischman' -p 'J0elTHEM4n1990!' --groups
```
- This is not very helpful - way too many default groups

Based on the services available on the machine, it's likely a domain controller. (Chat helped me identify the "classic DC fingerprint")
![[Pasted image 20250909205355.png]]

Using this NetExec command, I was able to verify that this machine is a domain controller.
```
nxc ldap 10.10.11.69 -u 'j.fleischman' -p 'J0elTHEM4n1990!' --dc-list
```
- Relevant output: ==DC01.fluffy.htb = 10.10.11.69==
### WinRM (wsman - port 5985) Login Attempt
WinRM is a Windows remote management protocol. Since it's open and we have authorized credentials, the obvious next step is to try to log in so we can "remotely manage" this box :)

`evil-winrm` is a tool used to log into machines by using WinRM from Kali.
```
evil-winrm -i 10.10.11.69 -u j.fleischman -p J0elTHEM4n1990!
```

Unfortunately, our user account doesn't have permission to log in via WinRM. By default, only administrators can use WinRM, so this is unsurprising. 
### PsExec (SMB - port 445) Login Attempt
Next, I tried logging into the machine by using PsExec, which is a lightweight remote management tool. 

```
impacket-psexec fluffy.htb/'j.fleischman':'J0elTHEM4n1990!'@10.10.11.69 cmd.exe
```
- Unfortunately, this also didn't work. 

![[Pasted image 20250909214245.png]]

Maybe I was wrong and one of the CVEs is the path to initial access. I'll have to look into that. 