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
