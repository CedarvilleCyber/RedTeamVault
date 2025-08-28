# Build Number and Version Information
```powershell
Get-WmiObject -Class win32_OperatingSystem | select Version,BuildNumber
```

# Get Powershell Execution Policies
```powershell
Get-ExecutionPolicy -List
```

# Get All Users and SIDs
```powershell
Get-WMIObject -Class win32_useraccount | select name,sid
```
