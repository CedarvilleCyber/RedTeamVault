TCP//445

SMB enumeration techniques. 

We want to find:
- Open SMB shares with known passwords or anonymous login
- SMB version information for potential exploitation

> [!warning]
> KNOW WHICH OF THESE ENUMERATIONS TEST VIA EXPLOITATION! Particularly thinking of the Nmap scripts...

- [[Smbclient]]
- [[SMBMap]]
- [[CrackMapExec]]
- [[Enum4Linux]]
- [[Nmap]] Scripts: 
	To run all the vuln scripts at once, use this scan ***with caution:*** 
	`sudo nmap -p139,445 -T4 --script smb-vuln* <ip-addr>`
	- smb-enum-users.nse
	- smb-enum-groups.nse
	- smb-enum-shares.nse
	- smb-os-discovery.nse
	- smb-vuln-ms06-025.nse
	- smb-vuln-ms07-029.nse
	- smb-vuln-ms08-067.nse
	- smb-vuln-ms10-054.nse
	- smb-vuln-ms10-061.nse
	- smb-vuln-ms17-010.nse
	- smb-vuln-cve-2017-7494.nse
- [[Metasploit]]
	- auxiliary/scanner/smb/smb_version

These scripts will identify EternalBlue, MS08-067 Netapi vulns, etc. 

# SMB Checklist
- Check all shares for guest access to any shares
- Enumerate SMB version, users, additional information with as many tools as possible.
- If vulnerabilities are present, exploit
- If not, start a bruteforce with known users in the background.

>[!note] 
>Recheck when new credentials are discovered!
