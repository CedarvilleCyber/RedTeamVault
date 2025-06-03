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