Tool for interacting with Remote Procedure Call for enumerating a machine or domain.

```shell
rpcclient -U "" -N <DC_IP> # Establish NULL session on AD DC

querydominfo # Get information about domain
querydompwinfo # Get only pw policy information
enumdomusers # Get domain usernames and RIDs
queryuser <RID> # Get information about user via RID
```

==The above commands will allow you to conduct password spraying much more effectively moving forward.==
