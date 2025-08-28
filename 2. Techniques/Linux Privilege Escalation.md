Tools:
[[PEASS|LinPEAS]]
[[LinEnum]]
[[LES]]
[[Linux Smart Enumeration]]
[[Linux Priv Checker]]
https://www.linuxkernelcves.com/cves

# Kernel Exploits

# Sudo 
- ALL:ALL (nopasswd) has happened to me before. Always check `sudo -l` to make sure you don't have root already, and use GTFObins to escalate if you have other perms. 

# SUID
- If a binary that appear in the GTFObins list is present here, follow the directions at the website and use it to escalate privileges as possible. 
```shell
find / -perm -4000 2>/dev/null # Search system for SUID binaries
```

# Capabilities
- TODO: Understand this

# Cron Jobs
- If a cron job executes a program at a writeable location, it can be replaced with a shell or other command to grant root access. You should be able to couple this with $PATH Hijacking OR writeable files/directories. 
```shell
crontab -l
cat /etc/crontab
```

# $PATH Hijacking
- If a SUID program or a cronjob runs a binary with a relative path, it can be replaced by something earlier in the PATH, or the PATH can be manipulated by the user with an active shell session to subvert the assumed PATH of the binary.

# NFS
- Examine /etc/exports for NFS shares with the no_root_squash attribute. This allows another machine to remotely mount the share and create a SUID program with root permissions to set the uid and gid to spawn a root shell.
