# Information Gathering/Enumeration
### Information Gathering
- OSINT
	- [[Passive Subdomain Enumeration]]
	- [[Passive Infrastructure Identification]]
- Active Information Gathering
	- [[Active Infrastructure Identification]]
	- [[Virtual Hosts]]

### Scanning
- [[Nmap]] is absolutely necessary. Get scan methodology down: 
	- Host discovery (if applicable) with manual ping sweep or with `-sn`
	- ~~Fast all-ports with [[Rustscan]] to get a quick listing~~
	- Fast all ports with Nmap to get port listing. Rustscan has proven to be too volatile. 
	- THEN open-ports scan with Nmap and NSE scripts. 
- [[Nessus]] 
	- Scanner that executes payloads against the target host. Use wisely, because this is not normal network traffic. Also, setup is expensive in terms of resources and time. 
	- Very good at fingerprinting services and specific vulnerabilities without lots of extra research
	- If range is a concern I cannot recommend [[OpenVAS]]
- Manual enumeration

### Enumerating/Attacking Common Services
- [[DNS]]
- [[FTP]]
- [[IMAP-POP3]]
- [[SMTP]]
- [[SMB]]
- [[SNMP]]
- [[RDP]]
- [[SQL Databases]]

### Initial Access
- Default Credentials
- Exploiting Vulnerable Software
	- [[Searchsploit]]
	- [[Metasploit]]
	- [[Exploit-DB]]
	- GitHub
	- [[Password Attacks|Password Attacks]]
- Client-Side Attacks/Social Engineering Attacks
	- [[BEEF]]
	- Maldocs
	- [[Phishing]]
- Web Applications
	- [[Cyber Team/RedTeamVault/Techniques/Web Applications/Start|Web Application Exploitation]]
- Domain Attacks
	- [[Active Directory|Active Directory Attacks]]
### Privilege Escalation
- [[Windows Privilege Escalation]]
- [[Linux Privilege Escalation]]
- [[Active Directory|Active Directory Attacks]]

### Post Exploitation
Utilize available techniques to stay hidden on the endpoint and penetrate deeper into the network while maintaining the highest privileges. 
- [[Evasion]]
- [[Persistence]]
- [[Pivoting and Lateral Movement]]
	- [[Wireless Operations]]
- [[File Transfers and Exfiltration]]
- [[Active Directory]]

### Reporting
- Use existing templates. If they are not available in the CPTC OneDrive then you should emulate a popular format like OffSec's. Plenty of OSCP report template generators out there. 
	- https://docs.sysreptor.com/
		- Can self-host for free, but limited to 3 users.
	- https://github.com/noraj/OSCP-Exam-Report-Template-Markdown
	- https://github.com/GhostManager/Ghostwriter
- Reports should be professional. As in largely white and in printable formats. None of this denim colored reporting anymore. That was embarrassing. 
- Template should be prepared beforehand. 

[[Additional Valuable Resources]]
