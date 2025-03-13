IDOR: Insecure Direct Object Reference. It's a type of access control vulnerability.

Tools:
- [[Burp Suite]]
- [[ZAProxy]]

This vulnerability occurs when a parameter is passed to the web server that is not checked once returned, and this parameter controls which of a list of options is returned to the user. 

`bank.com/user-information/1000` -> `bank.com/user-information/1001` 

In this example, using 1001 in place of 1000 (the original user ID), will return user 1001's data in a Direct Object Reference. This can happen in URL parameters, cookies, etc. If you are searching for non-sequential user ID's, use two accounts to test if they can reference data from each other. 

For more information on IDOR vulnerabilities, see the TryHackMe Junior Pentester Path. In its Intro to Web Hacking module, they have a room on IDOR vulnerabilities. 
