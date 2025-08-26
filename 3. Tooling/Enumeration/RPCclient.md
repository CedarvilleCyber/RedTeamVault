Tool for interacting with Remote Procedure Call for enumerating a machine or domain.

```shell
rpcclient -U "" -N <DC_IP> # Establish NULL session on AD DC

querydominfo # Get information about domain
querydompwinfo # Get only pw policy information
enumdomusers # Get domain usernames and RIDs
queryuser <RID> # Get information about user via RID

==Local Admin RID==: 0x1f4
==Domain Admin RID==: 0x200
```

==The above commands will allow you to conduct password spraying much more effectively moving forward.==

==Are you able to create users?== `createdomuser <username> [access mask]`