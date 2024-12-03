Bash one-liner for host discovery
```bash
for ip in {1..254}; do ping -c 1 -W 1 192.168.1.$ip &>/dev/null && echo "Host 192.168.1.$ip is up"; done
```
