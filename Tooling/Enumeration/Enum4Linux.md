Linux-based SMB enumeration tool. It doesn't enumerate Linux only! It just only works on Linux. 

| Command                                                  | Description                                                                                                           |
| -------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| `enum4linux -v target-ip`                                | Verbose mode, shows the underlying commands being executed by enum4linux                                              |
| `enum4linux -a target-ip`                                | Do Everything, runs all options apart from dictionary based share name guessing                                       |
| `enum4linux -U target-ip`                                | Lists usernames, if the server allows it - (RestrictAnonymous = 0)                                                    |
| `enum4linux -u administrator   -p password -U target-ip` | If you've managed to obtain credentials, you can pull a full list of users regardless of the RestrictAnonymous option |
| `enum4linux -r target-ip`                                | Pulls usernames from the default RID range (500-550,1000-1050)                                                        |
| `enum4linux -R 600-660 target-ip`                        | Pull usernames using a custom RID range                                                                               |
| `enum4linux -G target-ip`                                | Lists groups. if the server allows it, you can also specify username `-u` and password `-p`                           |
| `enum4linux -S target-ip`                                | List Windows shares, again you can also specify username `-u` and password `-p`                                       |
| `enum4linux -s shares.txt target-ip`                     | Perform a dictionary attack, if the server doesn't let you retrieve a share list                                      |
| `enum4linux -o target-ip`                                | Pulls OS information using smbclient, this can pull the service pack version on some versions of Windows              |
| `enum4linux -i target-ip`                                | Pull information about printers known to the remote device.                                                           |
| `enum4linux -P target-ip`                                | Get password policy for a host or domain.                                                                             |
Install with apt on Kali. Should come preinstalled.
