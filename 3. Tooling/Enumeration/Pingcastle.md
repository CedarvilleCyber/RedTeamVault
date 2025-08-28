[Documentation](https://www.pingcastle.com/documentation/)

Useful enumeration tool for sysadmins and red teamers alike

- Quick AD health assessment
- Non-disruptive
- Detects many configuration issues

```
PingCastle.exe --healthcheck --server mydomain.com
# Full health check

PingCastle.exe --scanner localadmin --server mydomain.com
PingCastle.exe --scanner nullsession --server mydomain.com
PingCastle.exe --scanner shares --server mydomain.com
# Specific security checks

```