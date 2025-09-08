initial scan: `sudo nmap <ip> -T4 -p- --open -oN init.nmap`

Creds: `j.fleischman / J0elTHEM4n1990!`

![[Pasted image 20250906141422.png]]

`smbclient -N -L \\<ip>`
![[Pasted image 20250906142009.png]]

![[Pasted image 20250906142547.png]]

We found a list of CVEs on the SMB server in the IT share. We then used exploit code 

`sudo responder -I tun0 -w -d -F` 