A tool for routing connections over HTTP or SOCKS proxies. This does NOT create proxies, but just routes tool/network traffic through existing proxies. Good proxies include SSH SOCKS4 proxies via the `-D` parameter or Meterpreter implants running under `autoroute` parms and a MSF SOCKS4 proxy overlaid. 

> [!tip]
> Make sure you understand your networking needs for your proxies. Metasploit callbacks must be modified to work correctly with a proxy, and will not use proxychains.
### 1. Add proxy details to /etc/proxychains4.conf
```shell
# socks5 127.0.0.1 9050
socks5 10.129.18.31 1080
```

### 2. Wrap your network command with proxychains
```shell
proxychains xfreerdp ...
proxychains firefox ...
proxychains curl ...
```
