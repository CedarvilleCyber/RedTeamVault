A tool for interacting with SMB shares

```shell
smbclient -L \\<IP> # List shares (requires a password)

echo exit | smbclient -U "" \\\\<IP>\\<SHARE> # Sometimes get a listing of files in a share

smbclient -U <user> \\\\<IP>\\<SHARE> # Interact with a share (authenticated)
```
