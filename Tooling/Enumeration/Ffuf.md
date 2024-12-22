FFuf bruteforces subdomains, directories, etc. 

`ffuf -w <wordlist> -u http://<IP> -H "HOST: FUZZ.<FQDN>" -fs 612` - VHost brute

`ffuf -w <wordlist> -u http://<IP>/FUZZ` - Dir brute

`ffuf -w <wordlist> -u http://FUZZ.s3.amazonaws.com` - S3 Bucket brute

`ffuf -w /usr/share/wordlists/SecLists/Usernames/Names/names.txt -X POST -d "username=FUZZ&email=x&password=x&cpassword=x" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.10.221.107/customers/signup -mr "username already exists"` - Example username enumeration brute

`ffuf -w valid_usernames.txt:W1,/usr/share/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 -X POST -d "username=W1&password=W2" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.10.221.107/customers/login -fc 200` - Example login brute

Install with apt on Kali.
