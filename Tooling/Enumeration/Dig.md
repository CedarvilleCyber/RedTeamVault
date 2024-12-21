Returns DNS records from a nameserver.
- `dig <FQDN> @<nameserver FQDN/IP` - standard (A records)
- `dig a <FQDN> @<nameserver FQDN/IP` - A records
- `dig aaaa <FQDN> @<nameserver FQDN/IP` - AAAA records
- `dig -x <IP> @<nameserver FQDN/IP` - PTR records
- `dig txt <FQDN> @<nameserver FQDN/IP` - TXT records
- `dig mx <FQDN> @<nameserver FQDN/IP` - MX records
- `dig soa <TLD>` - SOA records
- `dig ns <TLD> @<nameserver FQDN/IP>` - NS records
- `dig CH TXT version.bind <nameserver FQDN/IP>` - CHAOS TXT records
- `dig any <TLD> @<nameserver FQDN/IP>` - All records
- `dig axfr <TLD> @<nameserver FQDN/IP>` - DNS Zone Transfer

A zone transfer dumps all the records for everything managed by the nameserver. 
