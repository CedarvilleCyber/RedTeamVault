```
```A powerful post-exploitation enumeration and attack tool designed for **penetration testers and red teamers**. It simplifies and automates interaction with network services like **SMB, RDP, WinRM, MSSQL, LDAP**, and more.

Originally called **CrackMapExec (CME)**, NetExec is essentially a Python-based “Swiss Army knife” for assessing **Active Directory environments**, especially over **SMB**.
```

```shell
netexec smb 10.10.11.35
# shows OS, domain name, SMB signing

netexec smb 10.10.11.35 -u username -p 'Password123'
# check credentials

netexec smb 10.10.11.35 -U users.txt -P passwords.txt --no-bruteforce
# password spray

USER ENUMERATION:

netexec smb cicada.htb -u zaa -p '' --rid-brute
# use fake zaa account to enumerate users.

netexec smb 10.10.11.35 -u <user> -p 'ValidPassword' --users
# pulls a list of domain users

```