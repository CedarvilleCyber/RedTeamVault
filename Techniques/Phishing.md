Phishing is an important part of CPTC and penetraiton testing as a whole. While bypassing DKIM, SPF and other verification and filtering technology is largely out of scope, there are a plethora of tools and resources for getting past HR's pesky email filters. Useful tools for mass campaigns include [[GoPhish]]. 

>[!warning] 
>For CPTC you will be required to throw a payload via email with valid credentials. Make getting credentials a top priority early in the competition, and utilize them to further gain a foothold into the domain. Afterwards, you can use this foothold to send emails as a trusted source. 
>
>For this assignment, you are usually required to send an exe, xls or doc file. This can be prepared beforehand and quickly modified. Further, you should investigate other teams' GitHubs for good examples. Try and sneak stuff from successful teams though...

# Phishing for Initial Access
There are TONs of techniques for getting execution out of a phishing engagement. Some notable ones used in the wild include
- Macros! Word Documents, Spreadsheets, and MANY other M$ products support executing code via WScript or similar. Use this to your advantage. Sans AV intervention you can use this on its own to execute shellcode beacons via [[MSFVenom]] implants. However, newer versions of these products make them very difficult to deploy successfully. Evading these protections may or may not be in scope for CPTC, but be mindful of how difficult these will be to use in the real world. 
- Esoteric archive files (vmdk, other compressions) not extensively indexed in antivirus systems. Password-protected ZIPs are also a good bet, but the password has to be good to avoid mail's built-in virus scans. 
- Executables are usually super suspect. [[Evasion]] could help bypass basic host-based AVs but getting a user to run it could be difficult. Using a RTL override UTF-8 character to disguise filenames could also be useful. Ann\<RTL\>cod.exe -> Annexe.doc. Replacing an icon could also be useful in this situation. The primary use for this character is languages written from right to left like Arabic or Hebrew, but it can be abused here. 
- A popular technique today is clipboard hijacking. Auto-copy a powershell script to the user's keyboard when they enter a website and instruct them to use Win+R, Ctrl+V, Enter to perform some critically important task. 
- Polyglot files are another possible avenue. (need more information)
- Shortcut files can be used to run raw powershell, or to link users to new websites, etc. 

# Phishing for Credentials
 I am not familiar with the relationship between [[EvilGINX]] and [[GoPhish]]. If they interoperate well and are capable of filtering and whitelisting bypasses, this could be a powerful combination for snagging invaluable AD credentials for use inside the network. UPDATE: They do! kgretzky distributes a version of [[GoPhish]] configured to work with [[EvilGINX]] seamlessly. You still need a good pretense, but there are lots of those...

It is possible to perfectly clone and catch credentials passing through a copy of a popular login form. Perhaps LinkedIn or an SSO that the company uses. M$ is a safe bet. This can be further abused to forward the credentials to the service and send the user down the trail without ever being the wiser that their credentials were stolen. [[EvilGINX]] is useful for this purpose, as well as just using [[cURL]] to clone websites.  

>[!tip] 
>If your target utilizes MFA, not a problem. Simply capture their session cookie with [[EvilGINX]] on a successful login. For most places, that will get you access. However, Cedarville's network in particular appears to be a good example of how to prevent this, with VERY quick inactivity timeouts. 

# Pretext
Utilize standard social engineering tactics to get your target to follow links and make other poor decisions. 
- Urgency
- Authority
- Necessity
- Appeal to Kindness
Use these effectively and realistically. You should be phishing as a coworker or superior. Use what information is available from the domain to get information like employee hierarchy, etc. Another good input for this information is OSINT. For competitions, there is information seeded for you to find, and it will likely be looked for in the text of the email itself. 

# Infrastructure
Your sending server has to satisfy many qualifications to be seen as legitimate by the receiving mail server to avoid it being filtered out by spam filters. 

## SPF
TODO: Write this

## DKIM
TODO: Write this

## Spoofed Headers
TODO: Write this

You should consider setting up your own Postfix server for sending with granular control over your email payloads. 
