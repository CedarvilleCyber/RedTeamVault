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
