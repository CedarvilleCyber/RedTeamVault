AWESOME resource for graphically laying out a complex domain and identifying complex privilege escalation strategies through a network all the way to Domain Admin

>[!warning] 
>Make sure you use the correct collector version for the version of BloodHound you are using or the data will be incompatible and will not import.

Using the python distribution for Linux:
```shell
bloodhound-python -u <user> -p <password> -ns <DC_IP> -d <domain> -c all # Gather domain information
```
Using the C# distribution for Windows:
```powershell
.\SharpHound.exe -c All --zipfilename <outfile> # Gather domain informatio
```

Starting up the local examination server to parse the data returned from the top two. 
```shell
neo4j start # Start neo4j server hosting the web interface and database
sleep 60 # Give it time
bloodhound # Start up the interactive graphing software to query the database.
```

Bloodhound provides many analytical queries to show us the quickest path to the information we want. Some standouts:
- Find all Domain Admins
- Find Shortest Paths to Domain Admins
- Find Princpals with DCSync Rights
- Find Computers where Domain Users are Local Admin
- List all Kerberoastable Accounts
- Find Kerberoastable Users with most privileges

https://github.com/BloodHoundAD/BloodHound

# Custom Queries
```shell
MATCH p=(c1:Computer)-[r1:MemberOf*1..]->(g:Group)-[r2:AdminTo]->(n:Computer) RETURN p # Match computers whose accounts have AdminTo rights over other machines 
```
