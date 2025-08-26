
> [!danger] Detection Risk
> This tool bruteforces subdomains across only the resolvers that you provide. Gigabytes of DNS traffic is not subtle. 

Meta-query spider that speeds up resolution for subdomain bruteforcing. 
- `subbrute.py -p <TLD> -s <wordlist> -r <list of resolvers/nameservers>` - Standard usage

Returns data from ANY query, which means all records it could obtain. May or may not return results from CHAOS TXT records. Does NOT test for Zone Transfer privileges.

https://github.com/TheRook/subbrute
