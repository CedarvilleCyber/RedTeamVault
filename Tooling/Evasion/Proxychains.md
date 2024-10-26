A tool for routing connections over HTTP or SOCKS proxies. 

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
