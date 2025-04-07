# Unconstrained Delegation
Unconstrained delegation refers to the privilege afforded a service that is trusted to authenticate any other user to a service. This means that it retains the user's TGT in memory and is allowed in the AD privilege schema to impersonate them over the network. This is mostly bad because of the potential for ticket theft. 
```powershell
.\ADSearch.exe --search "(&(objectCategory=computer)(userAccountControl:1.2.840.113556.1.4.803:=524288))" --attributes samaccountname,dnshostname # Return a list of computers trusted for unconstrained delegation
```

# Exploiting Constrained Delegation

- [[Mimikatz]]
- [[PowerView]]
- [[Kekeo]]
- [[BloodHound]]
