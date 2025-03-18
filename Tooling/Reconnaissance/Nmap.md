Use for NSE scripting engine, and maybe network enumeration. ~~[[Rustscan]] is better for quickly enumerating open ports on a single device.~~ 

Nmap's full online manual can be found here - https://nmap.org/book/man.html

> [!danger] Detection Risk
> Use wisely if detection is a concern. Most competent AV/IDS/EDR can detect port scanning, and NAC will lock you out. A good counter is using the -T timing option to make sure that IPS does not have the resources to keep your scan packets in memory.

IDS/IPS evasion is covered in this list of flags. For example, use source port 53 to impersonate DNS and evade some poorly written firewall rules. 

## Scanning Options

| **Nmap Option**      | **Description**                                                        |
| -------------------- | ---------------------------------------------------------------------- |
| `-sn`                | Disables port scanning.                                                |
| `-Pn`                | Disables ICMP Echo Requests                                            |
| `-n`                 | Disables DNS Resolution.                                               |
| `-PE`                | Performs the ping scan by using ICMP Echo Requests against the target. |
| `--packet-trace`     | Shows all packets sent and received.                                   |
| `--reason`           | Displays the reason for a specific result.                             |
| `--disable-arp-ping` | Disables ARP Ping Requests.                                            |
| `--top-ports=<num>`  | Scans the specified top ports that have been defined as most frequent. |
| `-p-`                | Scan all ports.                                                        |
| `-p22-110`           | Scan all ports between 22 and 110.                                     |
| `-p22,25`            | Scans only the specified ports 22 and 25.                              |
| `-F`                 | Scans top 100 ports.                                                   |
| `-sS`                | Performs an TCP SYN-Scan.                                              |
| `-sA`                | Performs an TCP ACK-Scan.                                              |
| `-sU`                | Performs an UDP Scan.                                                  |
| `-sV`                | Scans the discovered services for their versions.                      |
| `-sC`                | Perform a Script Scan with scripts that are categorized as "default".  |
| `--script <script>`  | Performs a Script Scan by using the specified scripts.                 |
| `-O`                 | Performs an OS Detection Scan to determine the OS of the target.       |
| `-A`                 | Performs OS Detection, Service Detection, and traceroute scans.        |
| `-D RND:5`           | Sets the number of random Decoys that will be used to scan the target. |
| `-e`                 | Specifies the network interface that is used for the scan.             |
| `-S 10.10.10.200`    | Specifies the source IP address for the scan.                          |
| `-g`                 | Specifies the source port for the scan.                                |
| `--dns-server <ns>`  | DNS resolution is performed by using a specified name server.          |

## Input Options


| Nmap Option | Description                           |
| ----------- | ------------------------------------- |
| `-iL`       | Specify an infile of hosts or subnets |


## Output Options

| **Nmap Option** | **Description** |
|---|----|
| `-oA filename` | Stores the results in all available formats starting with the name of "filename". |
| `-oN filename` | Stores the results in normal format with the name "filename". |
| `-oG filename` | Stores the results in "grepable" format with the name of "filename". |
| `-oX filename` | Stores the results in XML format with the name of "filename". |

## Performance Options

| **Nmap Option**              | **Description**                                              |
| ---------------------------- | ------------------------------------------------------------ |
| `--max-retries <num>`        | Sets the number of retries for scans of specific ports.      |
| `--stats-every=5s`           | Displays scan's status every 5 seconds.                      |
| `-v/-vv`                     | Displays verbose output during the scan.                     |
| `--initial-rtt-timeout 50ms` | Sets the specified time value as initial RTT timeout.        |
| `--max-rtt-timeout 100ms`    | Sets the specified time value as maximum RTT timeout.        |
| `--min-rate 300`             | Sets the number of packets that will be sent simultaneously. |
| `-T <0-5>`                   | Specifies the specific timing template.                      |
| `--min-parallelism <num>`    | Sets the minimum number of parallel requests.                |

![[nmap-timing-options.png]]

## Standard Scans

```shell
nmap -sn -n -vv -oN <scan outfile> <subnet> # Subnet scan

nmap -T4 -p- -vv -oN <scan outfile> --disable-arp-ping -Pn -n <target> # Full port scan (slow, even with T4)

nmap -sC -sV -p <port1,port2> -vv -oN <scan outfile> <target> # Default scripts scan

nmap -p <port> --script=<service>* -oN <scan outfile> <target> # All scripts scan
```

## Example Stealth Scan
```shell
nmap --disable-arp-ping -n -g 53 -Pn --max-rate=10 -p <port> -oN <scan outfile> <target> -vv # Disabled ARP ping, ICMP ping, DNS resolution; spoofed source port, rate limited.
```

## NMAP Options by Category:
Nmap has a variety of different flags and options. It can do all of the steps below, although many of them are contingent on the flags you use.
![[nmap-scan-steps.png]]
Flags for each step:
### 1. Enumerate Targets
Default option - no flag needed. *Every* scan does this, so there is no flag for it.
### 2. Host Discovery
`-PR, -PE, -PP, -PM, -PS, -PA, -PU` All of these do host discovery. In order, they use the following protocols: ARP requests, ICMP echo, ICMP timestamp, ICMP netmask, TCP SYN, TCP ACK, and UDP. 

- **`-PR` uses ARP Requests. This is by far the most effective host discovery method, but it only works on machines that are on the same subnet as you are.** 
	- This is a default option when you run nmap with privileges (i.e. by using sudo)
- `-PE` uses ICMP echo requests, which are usually blocked by the firewall.
	- Used automatically when privileged (sudo)
- `-PP` uses ICMP timestamp requests.
	- Used automatically when privileged
- `-PM` uses ICMP netmask requests.
- **`-PS` uses TCP SYN packets. This is the most effective host discovery method for machines on a different subnet.** 
	- Used automatically when privileged
- `-PA` uses TCP ACK packets. 
	- Used automatically when privileged
- `-PU` uses UDP packets. This takes a while, but it's not a default option, so it may be beneficial in certain situations.
### 3. Reverse DNS Lookup
DNS resolution is interesting. It asks your local DNS server to give you the hostnames that correspond to the IP addresses you've discovered, which can be helpful.

By default, nmap performs DNS resolution on the hosts that you discover in step 2 (above). However, you can add the following flags to change nmap's DNS resolution behavior: 
- `-R` performs DNS resolution on all hosts, even if they seem to be offline or nonexistent.
- `-n` disables DNS resolution.
### 4. Scanning Ports
To understand this, you need to know what it means for a port to be open, closed, or filtered.
1. **Open**: indicates that a service is listening on the specified port.
2. **Closed**: indicates that no service is listening on the specified port, although the port is accessible. By accessible, we mean that it is reachable and is not blocked by a firewall or other security appliances/programs.
3. **Filtered**: means that Nmap cannot determine if the port is open or closed because the port is not accessible. This state is usually due to a firewall preventing Nmap from reaching that port. Nmap’s packets may be blocked from reaching the port; alternatively, the responses are blocked from reaching Nmap’s host.
4. **Unfiltered**: means that Nmap cannot determine if the port is open or closed, although the port is accessible. This state is encountered when using an ACK scan `-sA`.
5. **Open|Filtered**: This means that Nmap cannot determine whether the port is open or filtered.
6. **Closed|Filtered**: This means that Nmap cannot decide whether a port is closed or filtered.
#### Scan Types:
##### Recommended Scans:
- `-sT` is a TCP connect scan. Default if you don't have sudo privileges. Slow, but it gets the job done.
- `-sS` is a TCP SYN scan. It's like a TCP connect scan, but faster and stealthier. It uses a TCP packet with the SYN flag set.
	- Default when used with sudo. Requires sudo privileges.
- `-sU` is a UDP scan. It's slow, but it discovers UDP ports. It can only tell if a port is open/filtered vs closed, though. 
- `-sI` is an idle scan, also known as a "zombie" scan. In order for this scan type to work, there needs to be an idle machine attached to the target network.
	- ==Idle scans are the best way to evade IDS/IPS systems. Read the man page to figure out how to set them up.==
##### Other Scans:
- `-sA` is a TCP ACK scan. It uses a TCP packet with the ACK flag set. It requires sudo.
	- This is helpful to determine which ports are blocked by the firewall.
- `-S <spoofed-IP>` scans the network with a fake source IP address. This only works if you can capture the responses. For this to work, you need to disable host discovery by using the `-Pn` flag and specify the network interface using the `-e` flag.
- `--spoof-mac <spoofed-MAC>` This only works when you're on the same subnet as your target.
- `-D <DecoyIP-1>,<DecoyIP-2>,<more comma-separated decoy IPs>,ME` You can use RND to get nmap to generate random decoy IPs for you.
- `-f` fragments your packets, which *might* make your scan less likely to be detected by an IDS.

I don't recommend using the scans below because they would almost certainly be detected by an IDS/IPS. They don't adhere to the TCP standard, so their only conceivable use is for scanning a network. This makes them quite obviously "malicious" from an IDS/IPS perspective.
- `-sN` is a Null scan. It uses a TCP packet with no flags set.
- `-sF` is a FIN scan. It uses a TCP packet with only the FIN flag set.
- `-sX` is a Xmas scan. It uses a TCP packet with the FIN, PSH, and URG flags set.
### 5/6/7: Version Detection, OS Detection, Traceroute
Use the `-sV` flag for version detection. It's very loud.
- You can use `--version-intensity <num>` to set the intensity of the version scan, from 0 to 9. 0 is the lightest and 9 is the most detailed.
- The `-sV` flag performs banner grabbing by connecting to the service, so the version column is not a guess. It is definitive and correct. 

Use the `-O` flag for OS detection. (That's a capital o, not a zero.) Not always accurate, but it can be helpful. Take it with a grain of salt.

You can add the `--traceroute` flag to discover the routers between you and your target. If the `--traceroute` flag doesn't give any output, it's because there are no routers between you and the host. (For example, being directly connected to the target machine in THM or HTB.)
### 8. Nmap Scripting Engine (NSE)
To run nmap with the "default" scripts, you would use the `-sC` flag (for "scan with custom scripts").

To run a different group of scripts, or to run an individual script, you could use the --script flag. For example, if you wanted to run all of the scripts in the "safe" category, you would add --script=safe to your nmap command.

To discover if you can anonymously log into an FTP server on the target machine, you would run the ftp-anon script like so: `sudo nmap <target-ip> --script=ftp-anon`. You would never actually do this since ftp-anon is one of the default scripts that is run when you use the `-sC` flag, but I included it as an example of how to run an individual script.

![[nmap-script-categories.png]]
### 9. Saving Nmap Output
When you conduct a pentest, you need to document EVERYTHING you do. Thus, it is absolutely essential to save the output of your nmap scan into a file. There are a few different ways to do that:
- `-oN` outputs the scan's results into a normal, readable file. This is the one I would recommend using because it saves the results of  the scan in  the same format as you see on the screen when running nmap. Remember to add a file extension (.txt or .nmap).
- `-oG` saves the scan's results in a format that is designed to be grepped. This format is nice when you need to search through your output with `grep`, but it's hard to read.
- `-oX` saves the scan's results in XML format.
- `-oA` outputs the scan's results in all three formats. If you use this flag, make sure to put your scans in a `nmap` directory or a `scans` directory. When you run multiple scans with the `-oA` flag, your home directory gets cluttered quickly. 
# Useful Resources:
TryHackMe has a variety of rooms on nmap. Many of them are useful, but this group of four rooms is perhaps the most in-depth. These rooms are assigned in our Cyber Operations class, and they were the source of most of the information in this file.
1. [Nmap Live Host Discovery](tryhackme.com/r/room/nmap01)
2. [Nmap Basic Port Scans](tryhackme.com/r/room/nmap02)
3. [Nmap Advanced Port Scans](tryhackme.com/r/room/nmap03)
4. [Nmap Post Port Scans](tryhackme.com/r/room/nmap04)

If you need more information about any of nmap's features, I strongly recommend that you **read the man page.** ==`man nmap`== and then search with `/` and you'll be able to find what you need.
