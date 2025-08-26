Web Application Exploitation is an entire specialization of penetration testing. There is a LOT of material here.

[[Information Disclosure]]
[[IDOR]]
[[File Inclusion]]
[[File Upload]]
[[SQLi]]
[[XSS]]
[[CSRF]]
[[SSRF]]
[[SSTI]]
[[XXE]]
[[HTTP Request Smuggling]]
[[HTML Injection]]
[[Command Injection]]
[[Broken Authentication]]

Refer also to [[OWASP Top 10]] for reference.

It is very possible to disclose information on a web server by accident. Be sure to check for robots.txt entries, LFI/RFI, etc...

Methods exist also to execute code when authenticated to a web application as an administrative user. From injecting php backdoor into theme code or uploading malicious jar archives to Tomcat, there's a lot of ways to gain code execution. 

For fuzzing use [[GoBuster]] or [[Ffuf]] to get a good idea of the site layout. 
