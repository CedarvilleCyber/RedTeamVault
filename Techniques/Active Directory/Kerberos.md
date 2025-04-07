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

# NetNTLMv2 Relay Attack
Impossible with SMB and LDAP signing enabled. Allows MITMing of NetNTLMv2 authentication. 
- [[Impacket]]
