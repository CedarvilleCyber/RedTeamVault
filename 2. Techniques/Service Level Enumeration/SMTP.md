TCP//25

`telnet <IP/FQDN> 25`
NSE: smtp-open-relay

[[SMTP-User-Enum]]
[[Hydra]]

|**Command**|**Description**|
|---|---|
|`AUTH PLAIN`|AUTH is a service extension used to authenticate the client.|
|`HELO`|The client logs in with its computer name and thus starts the session.|
|`MAIL FROM`|The client names the email sender.|
|`RCPT TO`|The client names the email recipient.|
|`DATA`|The client initiates the transmission of the email.|
|`RSET`|The client aborts the initiated transmission but keeps the connection between client and server.|
|`VRFY`|The client checks if a mailbox is available for message transfer.|
|`EXPN`|The client also checks if a mailbox is available for messaging with this command.|
|`NOOP`|The client requests a response from the server to prevent disconnection due to time-out.|
|`QUIT`|The client terminates the session.|

# SMTP Checklist
- Scan for service version and accompanying data
- If vulnerable, exploit
- Enumerate valid usernames with smtp-user-enum as necessary
- Bruteforce for login credentials as necessary
- Enumerate inboxes for userful information and catalogue
