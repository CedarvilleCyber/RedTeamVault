ScareCrow packages shellcode inside binaries utilizing many known evasion methods to obfuscate, encrypt, and otherwise evade detection methods. Many techniques in [[Evasion]] are used, as well as more, like signing binaries, etc. 

```shell
./ScareCrow -I <shellcode path> -etw -domain www.microsoft.com # Basic shellcode packaging 
```

You can use flags to further customize your loader, including patching out [[ETW]] with the `-etw` flag, as well as evading sandbox detection with the `-sandbox` flag. However, these methods are always suspect, and you should always test your payloads before deploying them on the EDR they will be facing. 

ScareCrow can be found here: https://github.com/Tylous/ScareCrow
