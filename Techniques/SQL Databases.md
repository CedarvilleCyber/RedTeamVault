>[!note]
>This document does NOT cover SQLi attacks or anything related to them, nor does it cover SQL syntax. For SQLi help reference [[SQLi]]. 

TCP//3306 (MySQL)
TCP//1433 (MSSQL)

# SQL Database Checklist
- Enumerate version information
- Exploit if vulnerable
- Determine credentials
	- Bruteforce if no other recourse
- Enumerate valuable information
https://academy.hackthebox.com/module/116/section/1169

MySQL:
[[MySQL]]

MSSQL:
[[Sqsh/Sqlcmd]]

xp_cmdtree command execution, xp_dirtree and xp_subdirs hash stealing, local file read, user impersonation, linked servers, etc...
TODO: Make this readable and add details.