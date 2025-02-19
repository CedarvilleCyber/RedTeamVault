Rustscan is a faster [[Nmap]] alternative

> [!danger] Detection Risk
> Rustscan is very loud. If there is an active blue team or worthwhile IDS/IPS you will generate copious alerts and alarms that will hinder operations later.

>[!note] 
>Additionally, it is likely possible to make Nmap work quickly if necessary. As I have used Rustscan more I have liked it less. It is certainly a less stable version, and I am leaning more and more towards Nmap instead. 

# Multiple IP Scanning

You can scan multiple IPs using a comma separated list like so:

```shell
rustscan -a 127.0.0.1,0.0.0.0
```

# Host Scanning

RustScan can also scan hosts, like so:

```shell
➜ rustscan -a www.google.com, 127.0.0.1
Open 216.58.210.36:1
Open 216.58.210.36:80
Open 216.58.210.36:443
Open 127.0.0.1:53
Open 127.0.0.1:631
```

# CIDR support

RustScan supports CIDR:

```shell
➜ rustscan -a 192.168.0.0/30
```

# Hosts file as input

The file is a new line separated list of IPs / Hosts to scan:

**hosts.txt**

```
192.168.0.1
192.168.0.2
google.com
192.168.0.0/30
127.0.0.1
```

The argument is:

```
rustscan -a 'hosts.txt'
```

# Individual Port Scanning

RustScan can scan individual ports, like so:

```shell
➜ rustscan -a 127.0.0.1 -p 53
53
```

# Multiple selected port scanning

You can input a comma separated list of ports to scan:

```shell
➜ rustscan -a 127.0.0.1 -p 53,80,121,65535
53
```

# Ranges of ports
To scan a range of ports:

To run:

```shell
➜ rustscan -a 127.0.0.1 --range 1-1000    
53,631
```

# Adjusting the Nmap arguments

RustScan, at the moment, runs [[Nmap]] by default.

You can adjust the arguments like so:

```shell
rustscan -a 127.0.0.1 -- -A -sC
```

To run:

```shell
nmap -Pn -vvv -p $PORTS -A -sC 127.0.0.1
```

# Random Port Ordering

If you want to scan ports in a random order (which will help with not setting off firewalls) run RustScan like this:

```shell
➜ rustscan -a 127.0.0.1 --range 1-1000 --scan-order "Random"
53,631
```
