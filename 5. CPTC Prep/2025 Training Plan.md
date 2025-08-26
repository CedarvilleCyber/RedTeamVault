For freshmen or those with no offensive cyber experience, I recommend watching IppSec videos (YouTube) and taking notes. For those willing to get a TryHackMe subscription, their Junior Penetration Tester path is a good intro.

>[!important] Academy & Machines
>Use the HackTheBox [Academy to Labs](https://academy.hackthebox.com/academy-lab-relations) feature to determine which machines are related to the tactics you've been learning in the HTB Academy. This way, you can learn a tactic in HTB Academy, then practice it on a couple relevant HTB Machines.
# Exploitation Specializations
Create the six exploitation specializations. Three should be web, three should be AD.
## AD
Everyone should be familiar with NetExec (NXC).

AD team members may choose the training that suits their level of experience. It's expected that they engage in training, but which specific resources they use is up to their discretion. 

Team members with no AD experience are recommended to utilize the TryHackMe AD module instead of the HTB resources. Team members with AD experience or who have taken Cyber Ops are recommended to use the HTB AD Pentester job role path. 

Below is a list of recommended resources.
[HTB Intro to AD](https://academy.hackthebox.com/course/preview/introduction-to-active-directory)
[THM Active Directory Module](https://tryhackme.com/module/hacking-active-directory) - TryHackMe's best AD resource. Recommended for those with limited knowledge of AD. 
[HTB Active Directory Pentester](https://academy.hackthebox.com/path/preview/active-directory-penetration-tester) - the complete HTB AD job role path
[HTB Active Directory Enumeration](https://academy.hackthebox.com/path/preview/active-directory-enumeration) - module covers LDAP, PowerView, and BloodHound. 

All three AD team members should focus on understanding Kerberos authentication. 
### AD Team Member 1
Tools: [Rubeus](https://github.com/GhostPack/Rubeus), Impacket, Hashcat
### AD Team Member 2
Mimikatz
### AD Team Member 3
BloodHound & SharpHound
## Web
The web exploitation section will be split up between our three web-focused team members.

Burp Suite competency is required. 
They should also do the labs in their assigned PortSwigger Academy training course.

All training resources here (unless otherwise stated) link to PortSwigger Academy.
## Web Team Member 1
##### A01 Broken Access Control
- [Access Control](https://portswigger.net/web-security/access-control)
	- Includes **IDOR**
##### A02 Cryptographic Failures
- Make a list of 10ish [Common Weaknesses and Exposures](https://owasp.org/Top10/A02_2021-Cryptographic_Failures/#list-of-mapped-cwes) (CWEs) associated with Cryptographic Failures that you think we need to look for. Then figure out how to check for them.
	- Remember to send me the list once it's compiled. Make a section for how to find each type of CWE in the web app.
##### A03 Injection
- [SQL Injection](https://portswigger.net/web-security/sql-injection)
	- Use SQLmap
- [XSS](https://portswigger.net/web-security/cross-site-scripting)
- [Path Traversal](https://portswigger.net/web-security/file-path-traversal)
###### Optional Topics
- [OS Command Injection](https://portswigger.net/web-security/os-command-injection)
- [NoSQL Injection](https://portswigger.net/web-security/nosql-injection)
## Web Team Member 2
##### A04 Insecure Design
- [Business Logic Vulnerabilities](https://portswigger.net/web-security/logic-flaws)
- [Information Disclosure](https://portswigger.net/web-security/information-disclosure)
##### A05 Security Misconfiguration
- Make a list of 10ish [Common Weaknesses and Exposures](https://owasp.org/Top10/A05_2021-Security_Misconfiguration/#list-of-mapped-cwes) (CWEs) associated with Cryptographic Failures that you think we need to look for. Then figure out how to check for them.
	- Remember to send me the list once it's compiled. Make a section for how to find each type of CWE in the web app.
###### Optional Topics
- [XXE (XML Eternal Entity injection)](https://portswigger.net/web-security/xxe)
- [Clickjacking](https://portswigger.net/web-security/clickjacking)
##### A06 Vulnerable and Outdated Components
Find version numbers and check against `searchsploit`
- **Web server** version (ex: apache)
- **JS framework** version (ex: angular)
- **Libraries** used in their JS
## Web Team Member 3
[File Uploads](https://portswigger.net/web-security/file-upload) - it doesn't fit neatly into one of the OWASP top ten, but it's very important.
##### A07 Identification and Authentication Failures
- [Authentication](https://portswigger.net/web-security/authentication)
##### A08 Software and Data Integrity Failures
- CI/CD pipeline in the dev environment. 
- Do they use or rely on untrusted plugins, libraries, modules, repos, CDNs, etc.?
- [Insecure Deserialization](https://portswigger.net/web-security/deserialization) (difficult)
##### A09 Security Logging and Monitoring Failures
Once we get access to the web server machine:
- make sure they're logging
- write up a report block because they're not logging enough
##### A10 Server-Side Request Forgery (SSRF)
- [PortSwigger SSRF Path](https://portswigger.net/web-security/learning-paths/ssrf-attacks)
# Training Strategy
**When you practice, write a report on what you do.** If you don't know how to write a pentesting report, watch [this video](https://youtu.be/Ys5eIqS5pKw?si=AWrs8eGuEAD7tNQf) and remember that the executive summary is for non-technical people (i.e., CEOs) and the findings section is for the IT staff who need to fix the vulns you found.

As we train, we will go through multiple HTB machines at once to simulate the experience of hacking an interconnected network. Make sure you take notes and ***fill out the report as you go***.

Team members should familiarize themselves with the best online resources. We collected some in [[Additional Valuable Resources]]. 
- USE THESE RESOURCES when you practice and when we compete.
## Phishing Inject
https://github.com/dafthack/MailSniper