
### User Enumeration

##### Get all users
```
net user
```

```
net user /all
```

##### Logged on users
```
query user
```

##### Current user
```
echo %USERNAME%
```


### User Privs
```cmd-session
whoami /priv
```


### User Group Information
```cmd-session
whoami /groups
```

##### Get all groups
```
net localgroup 
```

##### Details about a group
```cmd-session
net localgroup [groupname]
```




### Password enumeration

##### Password Policy
```cmd-session
net accounts
```



```
findstr /si password *.txt
```
