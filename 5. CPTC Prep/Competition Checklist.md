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
- [ ] David M. - Nikto
- [ ] Nathaniel - Nuclei
- [ ] Nick - GreenBone // technically not but it fits well here
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