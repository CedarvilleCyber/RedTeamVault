**built-in Windows command-line tool** (on windows servers)

==It can be used to map network trusts==

### WHY does that matter? Because if you can't get in domain A, but you have a path into domain B, which domain A trusts, than you can ultimately compromise A.

```c

netdom query /domain:inlanefreight.local trust
# Get the trusts in this domain 

netdom query /domain:inlanefreight.local dc
# Query the domain controller

netdom query /domain:inlanefreight.local workstation
# Query workstations and servers

```