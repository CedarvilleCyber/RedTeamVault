CrackMapExec (or the new fork NetExec) is a very powerful tool for Windows domain enumeration and bruteforcing, with lots of options to play with. 

## Bruteforcing
```shell
crackmapexec <protocol> <IP> -u <username or userlist> -p <password or passwordlist> # Bruteforce a protocol with CME
```
## SMB Mode
```shell
crackmapexec smb <DC_IP> -u <username> -p <password> --users # Enumerate Domain Users
crackmapexec smb <DC_IP> -u <username> -p <password> --groups # Enumerate Domain Groups
crackmapexec smb <DC_IP> -u <username> -p <password> --loggedon-users # Enumerate Domain Users who are currently logged on
crackmapexec smb <DC_IP> -u <username> -p <password> --shares # Enumerate Domain shares

crackmapexec smb <DC_IP> -u <username> -p <password> -M spider_plus --share <share name> # Spider SMB share for all readable files
```

https://github.com/Pennyw0rth/NetExec
