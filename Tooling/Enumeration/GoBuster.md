GoBuster is a widely-used directory brute-forcing tool. We typically use it to discover hidden directories on web servers.

To download it, run `sudo apt install gobuster`. If it doesn't work, try `sudo apt update` followed by `sudo apt upgrade`, then try `sudo apt install gobuster` again. 

To use it to discover hidden directories on web servers, run this:
`gobuster dir -u <url or http://ipaddr:port> -w <path to your preferred wordlist>`

You should use one of the seclists wordlists. To get seclists, run `sudo apt install seclists` and you should have it. Its wordlists are in /usr/share/seclists.