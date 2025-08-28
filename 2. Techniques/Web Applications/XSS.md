The crux of XSS is the allowance to inject content into webpages as an end user. This is especially grievous if the content is persistent and stored in the database. XSS comes in two flavors: Stored and Reflected.

## Stored
Stored means that the XSS vulnerability is contained in code that stores data to the database. This means that the XSS will be viewed (and executed) by every user that visits the website. 
This is the most-used stored XSS test string: 

## Reflected
Reflected means that the XSS vulnerability is contained in form parameters or other data-passing locations. This means that an attacker can construct a request, and then entice an unsuspecting third party to send that request on behalf of the attacker to steal that third party's data. A reflected XSS vuln might appear in your inbox looking like this: `http://vulnerable-website.com/index.php?vuln=<script>alert('you got hacked');</script>`. This payload can be URL-encoded to appear like this:  `http://vulnerable-website.com/index.php?vuln=%3Cscript%3Ealert%28%27you%20got%20hacked%27%29%3B%3C%2Fscript%3E%60`. 

Tools:
- [[Burp Suite]]

