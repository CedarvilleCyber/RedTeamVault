Working through the Editor (easy linux machine)

Scanning
`sudo nmap <ip> -T4 -oN init.nmap`

Found ports `22, 80, 8080`

Check website, don't forget to add the host to the `/etc/hosts`

Two links documents and about, and download links on main page

When you click download you get a folder with a executable

Tried robots.txt

Used FUFF
`ffuf -c -u http://<ip>/FUZZ -w worldlist`
used /usr/share/wordlists/selections/
Found nothing of interest

Tried port 8080 on web got a wiki

Found an install instructions

Tried to use path traversal attack and was unsuccessful

Look into the version of the wiki found at the bottom of the page

Login page on the wiki
Tried logging in as Neal Bagwell

Tried sql injection on the login page and was unsuccessful 
`'1=1; --`

Tried FUFF again
`fuff -c -u http://wiki.editor.htb/xwiki/bin/FUZZ -w wordlist`
Found `admin, upload`
Locked behind login

Found in nmap scan with `-sC`
interesting page at `wiki.editor.htb/xwiki/bin/create/`
Tried adding a page
Couldn't get anything to work

Tried burpsuite

Potential tool `Username anarchy`
Used for brute forcing login information

Potential CVE: cve-2025-24893


