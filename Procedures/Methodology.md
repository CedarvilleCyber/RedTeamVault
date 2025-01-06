# 1. Reconnaissance
Conduct reconnaissance of the
- Domain
- Machines
- Applications/Services
- Accounts
Different tools for each. 

# 2. Enumeration
During this phase you should enumerate version information on individual services, search for specific vulnerabilities in exposed software, etc. This step is the most broad and most important during any engagement. 

Search for:
- Vulnerable Software Versions
- Default Credentials
- Misconfigurations
- Vulnerabilities in Web Applications
- Etc.

# 3. Exploitation
Here you actually throw your exploit and determine your effectiveness. Tools like [[Metasploit]] and [[Searchsploit]] will be helpful 

# 4. Post-Exploitation
There are three main portions to post-exploitation:
- Enumeration
- Exfiltration
- Privilege Escalation. 

Enumeration likely leads both to Privilege Escalation and Lateral Movement. During a penetration testing engagement, data exfiltration is NOT allowed, but you must prove access to data. 

# 5. Reporting
You must be able to provide a **detailed** and **reproduceable** explanation of your process. This MUST include screenshots of what you did - commands you ran, significant discoveries, that sort of thing. 

In a pentest, if your findings are not well-documented, then they didn't happen. ***Prove your results.***
