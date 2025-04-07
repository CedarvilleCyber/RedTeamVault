# Enumerating Domain Users
- [[LDAPSearch]]
- [[CrackMapExec]]
- [[RPCclient]]
- [[Enum4Linux]]
- [[Kerbrute]]
- [[Impacket]]
- [[ADSearch]]
- LOL Solutions (like the AD module)

We can also use a timing attack against Exchange servers without credentials. 
- [[Invoke-UsernameHarvestOWA]]


# Enumerating Security Controls

This Powershell will give lots of valuable information about the domain and your privileges within it. 

```powershell
Get-MpComputerStatus # Check for Windows Defender
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections # Get AppLocker Status
$ExecutionContext.SessionState.LanguageMode # Determines PowerShell Execution Language Mode (Constrained or Full access to PS functions)
Find-LAPSDelegatedGroups # Identify LAPS users and groups
Find-AdmPwdExtendedRights # Determine which devices you have access to LAPS on
Get-LAPSComputers # Find LAPS-enabled devices
```

- [[CrackMapExec]]
- [[RPCclient]]
- [[Impacket]]
- [[SMBMap]]
- [[WinDAPSearch]]
- [[BloodHound]]
- Powershell ActiveDirectory Module (Windows)
- [[PowerView]]
- [[Snaffler]]

```powershell
# PowerShell Active Directory Module

Get-Module # List modules available for import
Import-Module ActiveDirectory # Import the AD module if not already present
Get-ADDomain # Get lots of information about the domain
Get-ADUser -Filter {ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName # Get Kerberoastable users (Service Accounts)
Get-ADTrust -Filter * # Enumerate trust relationships within domain
Get-ADGroup -Filter * | select name # List AD groups
Get-ADGroup -Identity <groupname> # Get detailed group info
Get-ADGroupMember -Identity <groupname> # Get group members
```

# LOL Enumeration Solutions
- [[DSQuery]]

If you can attach your own Windows VM to the domain, you can use these techniques to enumerate from it with [[Runas]] to inject valid AD credentials. 

| **Command**                                             | **Result**                                                                                 |
| ------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| `hostname`                                              | Prints the PC's Name                                                                       |
| `[System.Environment]::OSVersion.Version`               | Prints out the OS version and revision level                                               |
| `wmic qfe get Caption,Description,HotFixID,InstalledOn` | Prints the patches and hotfixes applied to the host                                        |
| `ipconfig /all`                                         | Prints out network adapter state and configurations                                        |
| `set`                                                   | Displays a list of environment variables for the current session (ran from CMD-prompt)     |
| `echo %USERDOMAIN%`                                     | Displays the domain name to which the host belongs (ran from CMD-prompt)                   |
| `echo %logonserver%`                                    | Prints out the name of the Domain controller the host checks in with (ran from CMD-prompt) |
| `qwinsta`                                               | Print logged-on users                                                                      |

|**Networking Commands**|**Description**|
|---|---|
|`arp -a`|Lists all known hosts stored in the arp table.|
|`ipconfig /all`|Prints out adapter settings for the host. We can figure out the network segment from here.|
|`route print`|Displays the routing table (IPv4 & IPv6) identifying known networks and layer three routes shared with the host.|
|`netsh advfirewall show state`|Displays the status of the host's firewall. We can determine if it is active and filtering traffic.|

## WMI Checks
|**Command**|**Description**|
|---|---|
|`wmic qfe get Caption,Description,HotFixID,InstalledOn`|Prints the patch level and description of the Hotfixes applied|
|`wmic computersystem get Name,Domain,Manufacturer,Model,Username,Roles /format:List`|Displays basic host information to include any attributes within the list|
|`wmic process list /format:list`|A listing of all processes on host|
|`wmic ntdomain list /format:list`|Displays information about the Domain and Domain Controllers|
|`wmic useraccount list /format:list`|Displays information about all local accounts and any domain accounts that have logged into the device|
|`wmic group list /format:list`|Information about all local groups|
|`wmic sysaccount list /format:list`|Dumps information about any system accounts that are being used as service accounts.|
## Net Commands
| **Command**                                     | **Description**                                                                                                              |
| ----------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| `net accounts`                                  | Information about password requirements                                                                                      |
| `net accounts /domain`                          | Password and lockout policy                                                                                                  |
| `net group /domain`                             | Information about domain groups                                                                                              |
| `net group "Domain Admins" /domain`             | List users with domain admin privileges                                                                                      |
| `net group "domain computers" /domain`          | List of PCs connected to the domain                                                                                          |
| `net group "Domain Controllers" /domain`        | List PC accounts of domains controllers                                                                                      |
| `net group <domain_group_name> /domain`         | User that belongs to the group                                                                                               |
| `net groups /domain`                            | List of domain groups                                                                                                        |
| `net localgroup`                                | All available groups                                                                                                         |
| `net localgroup administrators /domain`         | List users that belong to the administrators group inside the domain (the group `Domain Admins` is included here by default) |
| `net localgroup Administrators`                 | Information about a group (admins)                                                                                           |
| `net localgroup administrators [username] /add` | Add user to administrators                                                                                                   |
| `net share`                                     | Check current shares                                                                                                         |
| `net user <ACCOUNT_NAME> /domain`               | Get information about a user within the domain                                                                               |
| `net user /domain`                              | List all users of the domain                                                                                                 |
| `net user %username%`                           | Information about the current user                                                                                           |
| `net use x: \computer\share`                    | Mount the share locally                                                                                                      |
| `net view`                                      | Get a list of computers                                                                                                      |
| `net view /all /domain[:domainname]`            | Shares on the domains                                                                                                        |
| `net view \computer /ALL`                       | List shares of a computer                                                                                                    |
| `net view /domain`                              | List of PCs of the domain                                                                                                    |


TODO: Finish this
