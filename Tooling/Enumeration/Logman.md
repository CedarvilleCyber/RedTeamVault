Logman queries [[ETW]] providers for information about executions on the target machine. 

```powershell
logman query providers # Query all ETW Providers on the system
logman query providers <Provider name or GUID> # Query details for one provider
logman query -ets # Query for active trace sessions
logman query <Trace Session> -ets # Query details for one trace session
```

