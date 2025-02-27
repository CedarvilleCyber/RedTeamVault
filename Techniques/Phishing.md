Phishing is an important part of CPTC and penetraiton testing as a whole. While bypassing DKIM, SPF and other verification and filtering technology is largely out of scope, there are a plethora of tools and resources for getting past HR's pesky email filters. Useful tools for mass campaigns include [[GoPhish]]. 

>[!warning] 
>For CPTC you will be required to throw a payload via email with valid credentials. Make getting credentials a top priority early in the competition, and utilize them to further gain a foothold into the domain. Afterwards, you can use this foothold to send emails as a trusted source. 
>
>For this assignment, you are usually required to send an exe, xls or doc file. This can be prepared beforehand and quickly modified. Further, you should investigate other teams' GitHubs for good examples. Try and sneak stuff from successful teams though...

# Phishing for Initial Access
There are TONs of techniques for getting execution out of a phishing engagement. Some notable ones used in the wild include
- Macros! Word Documents, Spreadsheets, and MANY other M$ products support executing code via WScript or similar. Use this to your advantage. Sans AV intervention you can use this on its own to execute shellcode beacons via [[MSFVenom]] implants. 

# Phishing for Credentials
 I am not familiar with the relationship between [[EvilGINX]] and [[GoPhish]]. If they interoperate well and are capable of filtering and whitelisting bypasses, this could be a powerful combination for snagging invaluable AD credentials for use inside the network. 
