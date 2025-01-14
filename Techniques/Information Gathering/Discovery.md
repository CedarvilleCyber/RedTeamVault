Bash one-liner for host discovery
```bash
for ip in {1..254}; do ping -c 1 -W 1 192.168.1.$ip &>/dev/null && echo "Host 192.168.1.$ip is up"; done
```

[[Nmap]] for host discovery
```shell
nmap -sn -v -n <subnet in CIDR>
```

Powershell for host discovery
```powershell
1..254 | ForEach-Object { Test-Connection -ComputerName ("192.168.1.{0}" -f $_) -Count 1 }
```

CMD for host discovery
```powershell
for /L %a in (1,1,254) do ping -n 1 192.168.1.%a
```

[[Tcpdump]] or [[Wireshark]] could easily be used to identify hosts on a network that are broadcasting or communicating with the attacker machine. 

[[Responder]] in passive mode could help to identify more infrastructure as well as IP addresses. 

Once you are inside a network use the arp cache and network information of compromised machines to identify new target infrastructure before ping sweeps or subnet scans, as this information is more authoritative. 
