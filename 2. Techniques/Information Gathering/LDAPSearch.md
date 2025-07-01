[[WinDAPSearch]]

LDAPSearch uses an LDAP Anonymous Bind session to enumerate password policy. 

`ldapsearch 172.16.5.5 -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "*" | grep -m 1 -B`

To list all users in a specific Organizational Unit (OU):

`ldapsearch -D "cn=admin,dc=example,dc=com" -w "password" -b "ou=users,dc=example,dc=com" "(objectClass=person)" cn`

==Find CA server!== [[Certipy]]
```shell
ldapldapsearch -x -H ldap://<dc-ip> -D '<username>@<domain>' -w 'Plmokn!?#123111'
  -b "CN=Enrollment Services,CN=Public Key Services,CN=Services,CN=Configuration,DC=<domain>,DC=<domain>"
```
'''
```

## ad-ldapdomaindump.py

a Python script designed to automate the process of extracting comprehensive information from Active Directory via LDAP. It's particularly useful for gathering a wide range of data in a structured format.

==Can also be used with other tools such as [[BloodHound]]==

```
Basic usage:

python3 ad-ldapdomaindump.py -u "username" -p "password" -d "example.com"
```

```
Output to a JSON file:

python3 ad-ldapdomaindump.py -u "username" -p "password" -d "example.com" -o "output.json"
```