For Help: [WadComs](https://wadcoms.github.io/)

A tool for interacting with SMB shares

```shell
smbclient -L \\<IP> # List shares (requires a password unless null auth (-N))

echo exit | smbclient -U "" \\\\<IP>\\<SHARE> # Sometimes get a listing of files in a share

smbclient -U "" -N \\\\<IP>\\<SHARE> # Interact with a share (null auth)
smbclient -U 'domain\<user>' \\\\<IP>\\<SHARE> # Interact with a share (authenticated)
```

Move files with `put` and `get`. 
