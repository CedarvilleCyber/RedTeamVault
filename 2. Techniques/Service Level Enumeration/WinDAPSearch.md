[Hacktricks](https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-ldap.html?highlight=windap#windapsearch)

> A Python-based tool designed for enumerating Active Directory (AD) objects—such as users, groups, and computers—via LDAP queries. It's particularly useful for low-privilege reconnaissance in Windows domains, allowing attackers to gather valuable information without requiring high-level credentials.

| Command                                                                                                | Explanation                              |
| ------------------------------------------------------------------------------------------------------ | ---------------------------------------- |
| python3 windapsearch.py --dc-ip 172.16.5.5 -u "" -U                                                    | # Try to enumerate with anonymous access |
| python3 windapsearch.py --dc-ip `<IP>` -u "domain\\user" -p "password" -U                              | # Enumerate ALL users:                   |
| python3 windapsearch.py --dc-ip `<IP>` -u "domain\\user" -p "password" --da                            | # Enumerate Domain Admins                |
| python3 windapsearch.py --dc-ip 192.168.1.10 -u "domain\\user" -p "password" -m "Remote Desktop Users" | # Enumerate Group Members                |
| ython3 windapsearch.py --dc-ip `<IP>` -u "domain\\user" -p "password" -PU                              | # Find privileged users                  |
