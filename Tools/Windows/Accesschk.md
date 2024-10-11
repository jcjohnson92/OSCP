https://docs.microsoft.com/en-us/sysinternals/downloads/accesschk

Reviewing permission of a particular named pipe
```cmd-session
accesschk.exe /accepteula \\.\Pipe\lsass -v
```


Attacking Named pipe
```cmd-session
accesschk.exe -accepteula -w \pipe\WindscribeService -v
```
