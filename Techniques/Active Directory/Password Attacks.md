# Enumerating Password Policies
- [[Enum4Linux]]
- [[CrackMapExec]]
- [[RPCclient]]

All of these tools rely on an SMB Null session to work if we do not already have domain credentials. From Windows we can try

```shell
net use \\<DC>\ipc$ "" /u:"" # Test if SMB null sessions are allowed 
```

- [[LDAPSearch]]
- [[PowerView]]

LDAPSearch uses an LDAP Anonymous Bind session to enumerate password policy. 

```shell
ldapsearch -h 172.16.5.5 -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "*" | grep -m 1 -B 10 pwdHistoryLength
```

From Windows is even easier.

```shell
net accounts
```

# Credential Injection
## Runas
```powershell
runas.exe /netonly /user:<domain>\<username> cmd.exe
```

With credential access and a Windows machine you can connect to the domain and use the Microsoft Management Console to enumerate machines and users in the environment. 
