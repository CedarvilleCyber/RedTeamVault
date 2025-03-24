If we have access to files we have uploaded (like a profile picture), we may be able to abuse this to call server-side executable code, likely PHP. Many different types of checks exist to verify that what has been uploaded matches what should be present. Three main ways of checking include
- File extension matching (Whitelist and blacklist)
	- This can potentially be bypassed prepending or appending a valid extension, depending on the webserver configuration. ex: shell.php.jpg or shell.jpg.php
	- Blacklists can also be bypassed using other executable file formats like .phar or .phtml
- Content-Type
	- This can be bypassed in [[Burp Suite]] by replacing the Content-Type manually with an image type. HOWEVER, it is easier to start with an allowed image type, brute allowed extensions by testing for differences in response message, and tailoring your payload appropriately. 
- Magic Bytes
	- Replacing the beginning bytes of the PHP file with an image file file signature will defeat this basic check. ex: `GIF8 <?php system(...` The easiest way to do this is to upload a small image file and replace all but the magic bytes. This ensures that they are copied correctly, because some of the bytes used do not always have clean copy-and-paste values. 

>[!tip] SVG images can also be used to provide LFI and potentially command injections (via [[XXE]]) or [[XSS]] if they are displayed unmodified in the webpage.
>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=index.php"> ]>
<svg>&xxe;</svg>
```

# Burp Intruder
Burp Intruder is highly useful for bruting parameters, allowed file extensions, etc. 
- `seclists/Discovery/Web-Content/web-extensions.txt`
- `seclists/Discovery/Web-Content/burp-parameters.txt`
The default options for Intruder URL-encode the `.` character, so be sure to turn this feature off when extension fuzzing. 
