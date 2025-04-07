This technology is of primary interest for Privilege Escalation, but it can be potentially abused other ways as well. 

The Data Protection API is used to guard user's credentials, and largely whenever data must be encrypted or decrypted in large blobs. 

Use `vaultcmd` to enumerate Windows users' credential vaults. 
```powershell
vaultcmd /list
vaultcmd /listcreds:"Windows Credentials" /all
```

We can also use [[Seatbelt]] to search for data in the Credential Vault. 

```powershell
Seatbelt.exe WindowsVault
Seatbelt.exe WindowsCredentialFiles
```

We can steal the decryption key for these credentials from LSASS with [[Mimikatz]] or use a process running as the owner to request them from the domain controller, also with [[Mimikatz]]. 


