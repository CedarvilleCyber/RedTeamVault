Kerbrute will perform guessing operations in an AD environment against a provided DC (Domain Controller).

```shell
kerbrute userenum -d <DOMAIN> --dc <DC_IP> <wordlist> -o <output of valid users>
kerbrute passwordspray -d <DOMAIN> --dc <DC_IP> <usernames_list> <password>
# TODO: Determine other uses and document
```

https://github.com/ropnop/kerbrute
