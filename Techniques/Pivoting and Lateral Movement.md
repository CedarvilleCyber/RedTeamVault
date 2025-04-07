Pivoting is the process of routing your attacking network traffic through an already-compromised host. This is useful for reaching vulnerable machines or services on an internal network from an external host. Pivoting and anonymization are different disciplines that use very similar tools for different reasons. Anonymization utilizes the traffic-tunneling behavior of our pivoting toolkit to route our traffic through anonymizing layers. [[Tor]] is a good example of using tunneling technology over an anonymizing layer. 

>[!note]
>This is a pet peeve of mine... VPNs do not really add lots of security in the world of TLS/SSL. What they are useful for is moving an exit point, similarly to Tor, which circumvents geolocation-related restrictions. Further, what they are *intented* for is moving the exit point *into* an organization's internal network where they can access intranet services. 

Below is a non-comprehensive listing and cheatsheet for pivoting and lateral movement through a network.

Tools:
- [[Proxychains]]
- [[Metasploit]]
- [[Meterpreter]]
- [[XFreeRDP]]
- [[Socat]]
- [[Rpivot]]
- [[Sshuttle]]
- [[Dnscat]]
- [[SocksOverRDP]]
- [[Ptunnel-ng]]
- [[Mimikatz]]
- [[Chisel]]
- [[Ligolo]]
- [[Plink]]
- [[Proxifier]]

[[Proxychains]] will be the most central tool for you here. Every one of these commands will create a proxy through which your connection needs to be routed. Sometimes it is accomplished for you, and other times you have to add a proxy via proxychains. A good advertisement for knowing which tools do what. 

# Lateral Movement and Pivoting in an AD Environment

## Remote Services
TODO: Fill in

## WMI
TODO: Fill in

## Hijacking User Behavior
- Hijack RDP sessions (2016 and earlier does not require password)
- Replace shared resources in writeable shares

## SSH Port Forwards
- Ye Old Faithful. Plink.exe contains all this functionality in Windows so if you upload it you can pivot with it with maximum overlap with Linux. 

## NTLM Relays
- You can use [[WinDivert]] to remap port traffic to allow it to be proxied outside of the environment. 
