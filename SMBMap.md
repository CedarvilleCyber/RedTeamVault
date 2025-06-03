For Help: [WadComs](https://wadcoms.github.io/)

#smb #enumeration
SMBMap is great for ==enumerating SMB shares== from a Linux attack host. It can be used to:
1. gather a listing of shares
2. permissions, and 
3. share contents if accessible. 

Once access is obtained, it can be used to download and upload files and execute remote commands.

==To get check access==:
```shell
smbmap -u <user> -p <password> -d <domain> -H <host>
```

``` shell
# recursively list files in a share
smbmap -u <user> -p <password> -H <host> -r 'share_name'

# download a file
smbmap -u <user> -p <password> -H <host> --download 'share_name\confidential.docx'

# search for a file matching a pattern
smbmap -u <user> -p <password> -H <host> -r 'share_name' -A '.*\.docx'

# search files for a keyword
smbmap -u <user> -p <password> -H <host> -r 'share_name' -F 'confidential'

# MUST BE ADMIN: you can even try to execute a command 
smbmap -u <user> -p <password> -H <host> -x 'whoami'
```

