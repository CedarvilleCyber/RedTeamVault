TCP//3389

If credentials are available or the server is unsecured simply login. 
However, [[Hydra]] has functionality for bruteforcing it as well. I am unaware of any high-profile CVEs for RDP but that may be my own lack of understanding. 

Brute with:
- [[Hydra]]
- [[Crowbar]]
- [[RDPassSpray]] (AD Environments)

Checklist
- Enumerate Credentials elsewhere and login
- Enumerate version information and search for exploitable vulnerabilities.
- Bruteforce with credentials. 
