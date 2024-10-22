Also known as path traversal, this is a technique used to access files that you're not allowed to view. Some websites allow users to input file names to upload website plugins, to specify the language, or other such things. This feature can be abused.

If the website doesn't have good input sanitization, then the user can perform path traversal by prepending the file name with ../ several times. This allows the user to get up to the root directory. Then, they can traverse back down and read whatever file they want. 

### Example:
Say a website provides a text box to allow users to select their language. The website has a version of their webpage in each major language, and these web pages are stored in /var/www/app/languages or a similar directory. However, the (malicious) user wants to view /etc/passwd. 

To do this, the user could put "../../../../etc/passwd" in the text box as though it was the name of their language. If the website has poor input sanitization and is hosted on a Linux server, it would simply display the /etc/passwd file. 

The ../ notation moves the program from the current directory into its' parent directory. Using ../ four times repeats the process until the program is at the top-level directory (root, or "/"). Then, the attacker used /etc/passwd to navigate to the passwd file so he or she could view it. 

>[!note]
> This traversal method is worth trying in URLs, as well. If you notice that a URL is specifying a file, it's worth trying path traversal in the URL itself.

### Anti-Path Traversal Filters
| Filter Method                                                                                                                                                                                                              | Bypass                                                                                                                                                                                                                                                                                                       |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| ==**File Type Specifier (ex: .php)**:== some programs append a file type, like .php, to the file name you put in. They do this to make it easier for their website to process the file name, but it breaks path traversal. | Follow your file name with the percent-encoded NULL byte, which is `%00`. This will prevent the program from appending the file type specifier. **Note: this bypass only works before PHP version 5.3.4**                                                                                                    |
| ==**Keyword Filtering:**== some programs filter their input by keyword. This helps prevent data exfiltration because files like /etc/passwd are blacklisted and can't be viewed by the user.                               | Follow your traversal path with a "." (ex: `../../../../etc/passwd/.`) because this can trick the filtration system into thinking you aren't trying to access a sensitive file. The period (".") refers to the current directory, so it doesn't change your traversal, but it defeats the filtration system. |
| ==**Dot-Dot-Slash Filtering:**== some filtration systems filter `../` out of their paths by replacing it with an empty string.                                                                                             | Instead of traversing with `../`, traverse with `....//` instead. This is because when the filtration system finds       "`....//`" in your string, it'll delete "`../`" from the middle of it, leaving "`../`" behind. This will allow your path traversal to work as normal.                               |
| **==Mandatory Starting Directory:==** some filtration systems force the file you input to be in a specific directory. Essentially, your path MUST include that directory, or the traversal won't work.                     | Simply figure out what the mandatory starting directory is and include it in your path. If the mandatory starting directory is `/languages`, then simply try `/languages/../../../../../etc/passwd`. It'll probably work, since you included the mandatory starting directory in your path.                  |

>[!note]
> It's entirely possible that you'll encounter a filtration system that combines multiple filtration methods. Don't give up after trying the basics- design your path traversals to defeat combinations of multiple filtration methods if necessary!

# Various Tactics:
### POST Request:
Generic form:
`curl http://<ip-addr>/dir/to/file.php --data-raw 'file=../../../../../directory/file.extension'

Example from THM: 
`curl http://10.10.194.104/challenges/chall1.php --data-raw 'file=../../../../../etc/flag1'`

You can also Inspect Element on a page, then change forms from GET requests to POST requests to achieve a similar effect. The `curl` example seems easier to replicate, though. 

### Cookies:
Sometimes websites use cookies to authenticate. Thus, users without the admin cookies can't see webpages that could be vulnerable. 

Generic form:
`curl http://<ip-addr>/dir/to/file.php --cookie "Cookie1=Value1; Cookie2=Value2" --data-raw 'file=../../../../directory/file.extension'

You can also edit and resend HTTP requests from the developer tools (Inspect Element) window, which allows you to change the cookies and accomplish what you need to. 


### Cookies:
THM example: `THM=../../../../etc/flag%00`
(This is the cookie for tryhackme.com/r/room/fileinc challenge question 2)



For futher research, see TryHackMe's File Inclusion room in the Intro To Web Hacking module of the Junior Pentesting Path. 