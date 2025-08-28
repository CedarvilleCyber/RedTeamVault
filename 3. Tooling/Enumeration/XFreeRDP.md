XFreeRDP is a Linux command-line tool used to interact with Remote Desktop on remote servers. It can use network authentication, proxies, smart cards, and even map drives to the target machine. 

```shell
xfreerdp /u:<username> /p:<password> /v:<IP> # Standard
xfreerdp /u:<username> /pth:<NTLM hash> /v:<IP> # Pass-The-Hash auth (Requires no Restricted Admin on target machine)
xfreerdp /u:<username> /p:<password> /v:<IP> /drive:<local path>,<remote name> # Map a local drive to the remote machine for easy tool/file transfers
```
