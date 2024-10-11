

### AV Enumerations
See if windows defender is running
```
query windefend
```

Query all services
```
query type= service 
```

Firewall settings
```
netsh advfirewall firewall dump
```

```
netsh firewall show state
```

```
netsh firewall show config
```

Check Windows 
```powershell-session
Get-MpComputerStatus
```

List Applocker Rules
```powershell-session
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections
```

Test Applocker Policy
```powershell-session
Get-AppLockerPolicy -Local | Test-AppLockerPolicy -path C:\Windows\System32\cmd.exe -User Everyone
```