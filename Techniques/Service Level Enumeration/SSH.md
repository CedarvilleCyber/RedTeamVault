TCP//22

SSH is used to log into devices remotely. It can use password or passwordless login. 

```shell
ssh root@<IP> # Log in with a password
ssh -i id_rsa root@<IP> # Log in with an identity file or private key (requires proper keyfile permissions)
```

Brute forcing is possible with [[Hydra]] or [[CrackMapExec]] without significant difficulty, but it is loud and it is slow. OpenSSH CVEs do exist, and are usually significant, but are few and far between. Don't spend hours on a username enumeration CVE that likely won't do us lots of good. 
