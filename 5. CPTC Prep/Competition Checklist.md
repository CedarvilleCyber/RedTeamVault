This is a CPTC checklist to help us when we forget command syntax. It'll keep us on track throughout the competition. When you start the competition, go through the checklist in a linear fashion. 

==Note: ADD SPECIFIC SYNTAX AND FLAGS for each command.== 

>[!important] Scan Outputs
>Make sure each scan outputs to a text file. Then, copy the scan outputs to a USB or to your local machine so we can use the scan outputs when writing our report after the competition. 
# Setup
- [ ] Run the tool setup script.
- [ ] Run the University of Florida logging script.
# Scanning
## Network Scanning
- [ ] `sudo nmap 10.10.0.0/16 -T4 -sn -oN ping.scan` 
- [ ] `sudo nmap <ip-addrs> -T4 -oN quick.scan` // do we feel this scan is necessary? is port.scan fast enough to skip quick.scan? 
- [ ] `sudo nmap <ip-addrs> -T4 -sC -sV -oN port.scan`
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
# Exploitation
## David M.
- [ ] Find web app versions and Google CVEs in those versions
- [ ] SSRF
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

