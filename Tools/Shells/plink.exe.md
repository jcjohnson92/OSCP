
https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html
Need a listen/listening port

```
# Port forward using plink
plink.exe -l root -pw mysecretpassword 192.168.0.101 -R [listening port]:127.0.0.1:[listening port]
```

```
# Port forward using meterpreter
portfwd add -l <attacker port> -p <victim port> -r <victim ip>
portfwd add -l 3306 -p 3306 -r 192.168.1.101
```

# How to Use

Need ssh server on kali
```
apt install ssh
```
change ssh_config file to allow 'PermitRootlogin yes'
![[Pasted image 20241010225741.png]]
```
service ssh restart
```

```
service ssh start
```

run plink on host machine
```
plinplink.exe -l root -pw mysecretpassword 192.168.0.101 -R 8080:127.0.0.1:8080
```

**May have to hit ENTER a bunch of times for command to go through

can use [[winexe]] to send commands
