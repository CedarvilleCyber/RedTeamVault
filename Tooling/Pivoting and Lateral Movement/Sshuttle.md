SSHuttle is a proxy/pivoting tool that automatically routes traffic through custom-built VPN-like performing SSH proxies. It performs similarly to SSH dynamic proxies, but does not require us to configure proxychains to use. Install with apt on Kali. 

```shell
sshuttle -r username@sshserver 0.0.0.0/0
```
- Use the `sshuttle -r` parameter to specify a remote server. On some systems, you may also need to use the `sshuttle -x` parameter to exclude sshserver or sshserver:22 so that your local machine can communicate directly to sshserver without it being redirected by sshuttle.
- By default sshuttle will automatically choose a method to use. Override with the `sshuttle --method`parameter.
- There is a shortcut for 0.0.0.0/0 for those that value their wrists: sshuttle -r username@sshserver 0/0    
- For "My VPN broke and need a temporary solution FAST to access local IPv4 addresses": ```
```shell
sshuttle --dns -NHr username@sshserver 10.0.0.0/8 172.16.0.0/12 192.168.0.0/16
```

If you would also like your DNS queries to be proxied through the DNS server of the server you are connect to:

```shell
sshuttle --dns -r username@sshserver 0/0
```

The above is probably what you want to use to prevent local network attacks such as Firesheep and friends. See the documentation for the `sshuttle --dns` parameter.
