
https://sushant747.gitbooks.io/total-oscp-guide/content/basics_of_windows.html


```
systeminfo
```

```
systeminfo | findstr /b /c:"OS Name" /c:"OS Versoin" /c:"System Type"
```


### Patches and updates

Powershell
```powershell-session
Get-HotFix | ft -AutoSize
```
CMD
```
wmic qfe
```

```
wmic qfe Caption,Description,HotFixID,InstalledOn
```

```
wmic logicaldisk
```

```
wmic logicaldisk get caption,description,providername 
```

```
wmic logicaldisk get caption
```

### Installed Programs
```cmd-session
wmic product get name
```

Powershell
```powershell-session
Get-WmiObject -Class Win32_Product |  select Name, Version
```

### Display Running Processes
```
netsat -ano
```
### Tasks
```cmd-session
tasklist /svc
```

### Environmental Variables
```
set
```



### Named Pipes
[[Pipelist]]
```cmd-session
 pipelist.exe /accepteula
```

##### Powershell
```powershell-session
gci \\.\pipe\
``````powershell-session
gci \\.\pipe\
```

Once a list is gather, enumerate that list with Accesschk
[[Accesschk]]
```cmd-session
accesschk.exe /accepteula \\.\Pipe\lsass -v
```

Attacking Named pipe
```cmd-session
accesschk.exe -accepteula -w \pipe\WindscribeService -v
```