TCP//UDP//53

Enumeration techniques for a local NS server. 

>[!note] DNS and DHCP are often handled by the AD Domain Controller inside an AD Domain

We are looking for 
- DNS records to give us more clear targeting information for the network
	- A zone transfer will give us the most complete network map

[[WHOIS]]
- Provides information about registration, ownership, etc...
[[NSLookup]]
- Returns actual DNS records from a nameserver
[[Dig]]
- Same as nslookup but only present on Linux by default and allows to select a nameserver by FQDN or IP. 

Subdomain bruteforcers 
- [[DNSEnum]] 
- [[SubBrute]] 

# DNS Checklist
- Enumerate information with dig. 
- Attempt zone transfer for maximum information
- Enumerate service version if possible
- If vulnerable, exploit (unlikely)
- If unable, perform subdomain bruteforcing with [[SubBrute]]. 
	- Pay special attention to TXT records and new subdomains. Bruteforce these too!
