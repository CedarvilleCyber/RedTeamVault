Command Injection occurs when a value from the user is used to execute a command on the server-side. If not properly checked and sanitized, it could be used to gain RCE on the web server. This is not unique to PHP, but exists in all server-side languages. 

- [[Burp Suite]]
# Common Injection Patterns
| **Injection Type**                      | **Operators**                                     |
| --------------------------------------- | ------------------------------------------------- |
| SQL Injection                           | `'` `,` `;` `--` `/* */`                          |
| Command Injection                       | `;` `&&`                                          |
| LDAP Injection                          | `*` `(` `)` `&` `\|`                              |
| XPath Injection                         | `'` `or` `and` `not` `substring` `concat` `count` |
| OS Command Injection                    | `;` `&` `\|`                                      |
| Code Injection                          | `'` `;` `--` `/* */` `$()` `${}` `#{}` `%{}` `^`  |
| Directory Traversal/File Path Traversal | `../` `..\\` `%00`                                |
| Object Injection                        | `;` `&` `\|`                                      |
| XQuery Injection                        | `'` `;` `--` `/* */`                              |
| Shellcode Injection                     | `\x` `\u` `%u` `%n`                               |
| Header Injection                        | `\n` `\r\n` `\t` `%0d` `%0a` `%09`                |

# Bypassing Filters
[[PayloadsAllTheThings]] is a great resource for methods of bypassing space filters, and many other injection techniques.  [[Bashfuscator]] is also a good resource for obfuscating bash injections. [[DOSfuscator]] is the Windows equivalent.
