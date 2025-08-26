DSQuery is a Windows utility distributed by default and often used in AD environments. It is a LOL solution with similar functionality to [[BloodHound]] and [[PowerView]]. 

```powershell
dsquery user # Search for domain users
dsquery computer # Search for domain-joined machines

dsquery * "CN=Users,DC=INLANEFREIGHT,DC=LOCAL" # Wildcard search for al objects in OU
dsquery * -filter "(&(objectCategory=person)(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=32))" -attr distinguishedName userAccountControl # Search for all users with PASSWD_NOTREQD set using userAccessControl
dsquery * -filter "(userAccountControl:1.2.840.113556.1.4.803:=8192)" -limit 5 -attr sAMAccountName # Search for all Domain Controllers, limit 5
```

![text](https://academy.hackthebox.com/storage/modules/143/UAC-values.png)
The above image details how to effectively use userAccountControl filtering notation. There are only three "main" rules for searching. 

#### OID match strings

OIDs are rules used to match bit values with attributes, as seen above. For LDAP and AD, there are three main matching rules:

1. `1.2.840.113556.1.4.803`

When using this rule as we did in the example above, we are saying the bit value must match completely to meet the search requirements. Great for matching a singular attribute.

2. `1.2.840.113556.1.4.804`

When using this rule, we are saying that we want our results to show any attribute match if any bit in the chain matches. This works in the case of an object having multiple attributes set.

3. `1.2.840.113556.1.4.1941`

This rule is used to match filters that apply to the Distinguished Name of an object and will search through all ownership and membership entries.

#### Logical Operators

When building out search strings, we can utilize logical operators to combine values for the search. The operators `&` `|` and `!` are used for this purpose. For example we can combine multiple search criteria with the `& (and)` operator like so:  
`(&(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=64))`

The above example sets the first criteria that the object must be a user and combines it with searching for a UAC bit value of 64 (Password Can't Change). A user with that attribute set would match the filter. You can take this even further and combine multiple attributes like `(&(1) (2) (3))`. The `!` (not) and `|` (or) operators can work similarly. For example, our filter above can be modified as follows:  
`(&(objectClass=user)(!userAccountControl:1.2.840.113556.1.4.803:=64))`

This would search for any user object that does `NOT` have the Password Can't Change attribute set. 

Credit: https://academy.hackthebox.com/module/143/section/1360

