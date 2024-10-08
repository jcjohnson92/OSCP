
ntlm
```
hashcat -m 5600 ntlm.txt /usr/share/wordlists/rockyou.txt

```

krb5tgs hash crack
```shell-session
hashcat -m 13100 sqldev_tgs_hashcat /usr/share/wordlists/rockyou.txt 
```


```shell-session
hashcat -m 19700 aes_to_crack /usr/share/wordlists/rockyou.txt 
```

$krb5asrep$23
```
hashcat -m 18200 sqldev_tgs_hashcat /usr/share/wordlists/rockyou.txt 
```