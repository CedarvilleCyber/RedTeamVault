ETW is Event Tracing for Windows and is a much more full-featured logging solution than even Windows Event Logs.  The CLI tool used to interact with these traces is called [[Logman]], but we can also use Sysinternals Performance Monitor or a tool called Message Analyzer. There is also a Powershell namespace by the name of `System.Diagnostics.Eventing.Reader` that interacts with ETW as well. 

>[!note] Windows Logging
>Windows logging is already verbose. It may take significant effort to defeat Event Viewer and ETW.

Evading ETW and by extension Windows logging at large is discussed in [[Evasion]].

This note largely exists to remind operators that ETW does in fact exist and is watching everything that happens on every Windows box. 
