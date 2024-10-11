

### Did you run these first for automation
- [ ] [[Automation tools]]

### Manual Enumeration
- [ ] [[System Info]]
	- [ ] Get systeminfo
		- [ ] OS
		- [ ] Hostname
		- [ ] Verison
		- [ ] Running Services
		- [ ] Tasks running
			- [ ] software installed
			- [ ] tasks running
		- [ ] Environmental Variables
			- [ ] possible file shares
			- [ ] Home Drive
		- [ ] Configuration Information
			- [ ] hotfixes
			- [ ] boot time
			- [ ] os version
			- [ ] dual hommed
		- [ ] Patches and Updates
		- [ ] Installed Programs
		- [ ] Open ports running/services
		- [ ] named pipes
- [ ] [[Network Enumeration]]
	- [ ] network adapters
	- [ ] ip space
	- [ ] mac address
	- [ ] arp table
	- [ ] routing table
- [ ] [[Antivirus Enumeration]]
	- [ ] windows defender
	- [ ] applocker
- [ ] [[User Enumeration]]
	- [ ] usernames
	- [ ] passwords
- [ ] [[Check Priviliedges]]
	- [ ] 


### Kernel Exploit
[https://github.com/SecWiki/windows-kernel-exploits](https://github.com/SecWiki/windows-kernel-exploits)
- [ ] Run Suggestor
- [ ] run other automation tools 
- [ ] find kernal exploits and attempt to run
- [ ] manual 
	- [ ] Get a reverse shell
		- [ ] [[msfvenom]]
		- [ ] run nc listener
		- [ ] run [[windows-exploit-suggestor.py]]
			- [ ] google search for kernal exploit if found
		- [ ] 

### Password Hunting
- [ ] [[Password Hunting]]
- [ ] If you find a password, try password resuse with all accounts
	- [ ] password spray it, could be admin password
- [ ] 

### Portforwarding with remote code
- [ ] Create Port forwarding with [[plink.exe]]
	- [ ] use [[winexe]] to send remote code

### SeImpersonate and SeAssignPrimaryToken
- [ ] Connect with [[MSSQLClient.py]]
	- [ ] run whoami and priv
	- [ ] looks at priv for Impersonate
	- [ ] Run [[JuicyPotato]]
	- [ ] Can run [[PrintSpoofer.exe]] as well

### SeDebugPrivilege
- [ ] Is DeDebugPrivildege listed
	- [ ] run whoami /priv
- [ ] Use [[Procdump]]
- [ ] Cant load tools but have RDP access
	- [ ] [[LSASS Dump]]
- [ ] [[REC DeBugPriv]]
- [ ] [[SeTakeOwnershipPrivilege]]
	- [ ] look for files
	- [ ] passwords
	- [ ] configs and more
