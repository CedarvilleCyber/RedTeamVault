TCP//21

FTP enumeration techniques

We are looking for open FTP shares with authenticated or guest access.

Commands for ftp command line tool. 

Checklist:
- Check for anonymous access
- Check for vulnerabilities
- Bruteforce

|**Commands**|**Description**|
|---|---|
|`connect`|Sets the remote host, and optionally the port, for file transfers.|
|`get`|Transfers a file or set of files from the remote host to the local host.|
|`put`|Transfers a file or set of files from the local host onto the remote host.|
|`quit`|Exits tftp.|
|`status`|Shows the current status of tftp, including the current transfer mode (ascii or binary), connection status, time-out value, and so on.|
|`verbose`|Turns verbose mode, which displays additional information during file transfer, on or off.|
|`debug`| Turns on debug mode and prints more data|
|`trace`| Turns on packet tracing|

Connect to an FTP server - `ftp 10.129.14.136`
Download all available files - `wget -m --no-passive ftp://anonymous:anonymous@10.129.14.136`

[[Nmap]] Scripts
