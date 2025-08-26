**Actively enumerates AD Group Policy Objects (GPOs)** for attack-oriented misconfigurations via LDAP and SYSVOL analysis

Detects exploitable issues like:
- Inclusion of Domain Users in high-privilege groups
- Weak ACLs on scripts or MSI packages
- Embedded credentials in policies or outdated “morphed” files
==No admin needed!!==

```
# Output to console
group3r.exe -s

# Output to log file
group3r.exe -f group3r.log

# Specify DC and domain if not joined:
group3r.exe -c 10.0.0.5 -d mydomain.local -s -f group3r.log

# Show only findings with issues
group3r.exe -s -w

# Limit to severity level 4 (highest)
group3r.exe -s -a 4

# Show only enabled policy settings
group3r.exe -s -e

# Skip old/morphed files
group3r.exe -s -r

# Offline SYSVOL analysis
group3r.exe -o -y C:\SYSVOL\backup -s
```