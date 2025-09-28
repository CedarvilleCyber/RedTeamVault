This is a CPTC checklist to help us when we forget command syntax. It'll keep us on track throughout the competition. When you start the competition, go through the checklist in a linear fashion. 

==Note: ADD SPECIFIC SYNTAX AND FLAGS for each command.== 

>[!important] Scan Outputs
>Make sure each scan outputs to a text file. Then, copy the scan outputs to a USB or to your local machine so we can use the scan outputs when writing our report after the competition. 
# Setup
- [ ] Run the tool setup script.
- [ ] Run the University of Florida logging script. (Is this how we are going to track every command?)
- [ ] CPTC will provide us with a OneDrive account. That's where we will store all our notes and collaboration resources (including the final report). 
	- [ ] Make a word doc for each host in the environment and section it off by service
- [ ] We should get a big general password list together. ==**ALWAYS try default creds first**==
	- Do some OSINT to make a custom password list.
	- `/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt` (ffuf/gobuster)
	- `/seclists/Usernames/top-usernames-shortlist.txt` (usernames for login attempts)
	- `/seclists/Passwords/Leaked-Databases/rockyou.txt` (passwords)
- [ ] Get a **network map on the whiteboard for visualization,** that helps a ton
# Scanning
## Network Scanning
- [ ] `sudo nmap 10.10.0.0/16 -T4 -sn -oN ping.scan` 
- [ ] `sudo nmap <ip-addrs> -T4 -p- --open -oN allports.scan`
- [ ] `sudo nmap <ip-addrs> -T4 -sC -sV -oN port-version.scan`
- [ ] `sudo nmap <ip-addrs> -T4 -sU -oN udp.scan`
- [ ] `sudo nmap -p- -A -T4 -sV --script vuln <target-ip> -oN in-depth.scan` // start the scan and move on
## Network Version Enumeration
Now we have version numbers. Use SearchSploit and the CVE database to find CVEs for all services.
## Web App Scanning
- [ ] `nikto -host <url-or-ip> -o nikto.txt`
	- David
- [ ] `nuclei -u <url-or-ip> -o nuclei.txt`
	- Nathaniel
- [ ] GreenBone // technically not but it fits well here
	- Nick
## AD Enumeration
- [ ] John - SMB share enumeration
- [ ] Josiah - SMB and LDAP user enumeration
- [ ] Lamoreaux - check Kerberos pre-auth and start Kerberos attacks
## Lamoreaux - AD Plan:
1. `ldapsearch` and `rpcclient` to see if anonymous binding is allowed
2. [[enum4linux]], crackmapexec
	1. `enum4linux -a <target-ip>`
3. Attempt to get a list of valid users, try pre-auth, try to kerberoast (`kerbrute`, `impacket`)
4. Try to harvest credentials with GPP, XML files, with nxe
5. get a session on a windows machine
6. download bloodhound and run a query
7. privesc
8. Look for more creds to spread to other machinesPP

# Exploitation
## David M.
- [ ] Find web app versions and Google CVEs in those versions (https://www.cve.org/)
	- [ ] find via Metasploit (`search <thing-version>`) or download PoC exploit from GitHub
- [ ] SSRF
### Password Spraying w/ Hydra
Hydra's syntax can be unintuitive, so look it up.
- `-L` specifies a username wordlist
- `-P` specifies a password wordlist 
- `-o` outputs to a file
- `-t` to specify a number of **threads** (speeds things up)

To use Hydra against FTP (Password Guessing):
`hydra -l username -P /path/to/passlist ftp://<ip-addr>`

SSH and SMTP share the same syntax, but with `ssh://` or `smtp://` instead of `ftp://`

>[!warning] Examine the Login Request
>Login requests vary in formatting. If you don't get the format right, your password spraying won't work. Use BurpSuite to examine the login page and failure response text (ex: "invalid login" or "wrong creds") before you try Hydra.
>
>Doing so will make it easier to get Hydra to work, since you need to know how the authentication works and what result you get on an invalid login to set up Hydra correctly.

Examples for HTTP(S):
```shell
# example for GET requests
hydra -L /usr/bin/seclists/Usernames/top-usernames-shortlist.txt \
-P usr/bin/seclists/Passwords/Leaked-Databases/rockyou.txt \
login.example.com http-get-form "/login.php?username=^USER^&password=^PASS^:Login failed"

# example for POST requests
hydra -L /usr/bin/seclists/Usernames/top-usernames-shortlist.txt \
-P usr/bin/seclists/Passwords/Leaked-Databases/rockyou.txt \
login.example.com http-post-form "/login.php:username=^USER^&password=^PASS^:Login failed"
```

Tips:
- Ask ChatGPT to explain the syntax if you're confused
- Always double-check the **form parameters** in Burp Suite or browser dev tools. Field names often aren’t just `username` and `password`. They might be `user`, `pwd`, `login`, etc.
- The **failure string** is critical. If you get it wrong, Hydra will either say “all guesses worked” or “all failed.” Look at the HTTP response to copy a reliable error message.

## Nathaniel
==CHOOSE attacks you want to try from the 2025 Training Plan.==
## Nick
==CHOOSE attacks you want to try from the 2025 Training Plan.==
# Privilege Escalation
## Linux
`grep sh /etc/passwd | cut -d ":" -f 1 > users.txt` makes a user list for brute-forcing

>  https://gtfobins.github.io/ - a list of native binaries for privilege escalation.

GTFOBins is basically an index of Linux PrivEsc methods. How to use it:
1. Check for sudo privileges on the victim machine: `sudo -l`
2. Check for SetUID executables: `find / -perm -4000 2>/dev/null`
3. Check for executables with capabilities: `getcap -r / 2>/dev/null`
4. Check the capabilities of your user: `capsh --print`
5. ==Look up ***all*** of your findings on GTFOBins.==
## Windows
List saved credentials:
`cmdkey /list`

Use saved credentials:
`runas /savecred /user:admin cmd.exe`

https://lolbas-project.github.io/ - a list of native binaries, scripts, and libraries for PrivEsc.
## Both
Get kernel version w/ `uname -a` (Linux) or `systeminfo` (Windows), then search for CVEs here:
- https://github.com/SecWiki/linux-kernel-exploits
- https://github.com/SecWiki/windows-kernel-exploits
# Lateral Movement Options
## Linux 
- **SSH** (port 22) `ssh user@ip`
## Windows
**RDP** (port 3389) `xfreerdp /v:<ip> /u:<user> /p:<password>`

**PsExec** (445 - SMB)
- On Windows: `PsExec.exe \\x.x.x.x -u Administrator -p Password -i cmd.exe`

- On Kali: `impacket-psexec domainName/user:password@x.x.x.x`
- (or use `psexec.py` instead of `impacket-psexec`)

**WinRM** (port 5985 or 5986)

- From Windows, there are two methods: 
- `winrs -u:UserName -p:Password -r:https://x.x.x.x cmd.exe`
- `Invoke-Command -Computername TARGET -Credential $credential -ScriptBlock {whoami}`

From Kali: 
- `evil-winrm -i interfaceNum -u username -p password`
	- Add the -S flag to connect on 5986 via HTTPS instead of 5985 via HTTP

**Windows Management Instrumentation (WMI)**
Requirements to use: 
All of these ports must be open for this to work.
- 35/TCP DCOM  
- 445/TCP SMB  
- 49152-65535/TCP DCERPC  
- Must be an administrative user

WMI on Kali: 
- `impacket-wmiexec user:password@x.x.x.x`

SMBexec (port 445)
- `impacket-smbexec domainName/user:password@x.x.x.x`

>[!success] Lateral Movement with Pass-The-Hash
>Mimikatz, Impacket, and Metasploit allow you to use pass-the-hash to start a reverse shell just with the user's hash. You don't even need to crack it!

