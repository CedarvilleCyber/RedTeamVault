VirusTotal DNS replication - 
Censys.io CT logs - 
Crt.sh CT logs - 

```shell
openssl s_client -ign_eof 2>/dev/null <<<$'HEAD / HTTP/1.0\r\n\r' -connect "${TARGET}:${PORT}" | openssl x509 -noout -text -in - | grep 'DNS' | sed -e 's|DNS:|\n|g' -e 's|^\*.*||g' | tr -d ',' | sort -u # Quick and dirty subdomain enumeration on target

curl -s "https://crt.sh/?q=${TARGET}&output=json" | jq -r '.[] | "\(.name_value)\n\(.common_name)"' | sort -u > "${TARGET}_crt.sh.txt" # Quick and dirty subdomain enumeration via crt.sh

##### Harvester Subdomain Enumeration
cat sources.txt | while read source; do theHarvester -d "${TARGET}" -b $source -f "${source}_${TARGET}";done # Enumerate subdomains using modules specified in `sources.txt` and store in a json. 
cat *.json | jq -r '.hosts[]' 2>/dev/null | cut -d':' -f 1 | sort -u > "${TARGET}_theHarvester.txt" # Parse JSON files to text. 
cat facebook.com_*.txt | sort -u > facebook.com_subdomains_passive.txt # Merge all subs into a list
```
