
### Overview of Permisisons

#### Rights with windows

|**Group**|**Description**|
|---|---|
|Default Administrators|Domain Admins and Enterprise Admins are "super" groups.|
|Server Operators|Members can modify services, access SMB shares, and backup files.|
|Backup Operators|Members are allowed to log onto DCs locally and should be considered Domain Admins. They can make shadow copies of the SAM/NTDS database, read the registry remotely, and access the file system on the DC via SMB. This group is sometimes added to the local Backup Operators group on non-DCs.|
|Print Operators|Members can log on to DCs locally and "trick" Windows into loading a malicious driver.|
|Hyper-V Administrators|If there are virtual DCs, any virtualization admins, such as members of Hyper-V Administrators, should be considered Domain Admins.|
|Account Operators|Members can modify non-protected accounts and groups in the domain.|
|Remote Desktop Users|Members are not given any useful permissions by default but are often granted additional rights such as `Allow Login Through Remote Desktop Services` and can move laterally using the RDP protocol.|
|Remote Management Users|Members can log on to DCs with PSRemoting (This group is sometimes added to the local remote management group on non-DCs).|
|Group Policy Creator Owners|Members can create new GPOs but would need to be delegated additional permissions to link GPOs to a container such as a domain or OU.|
|Schema Admins|Members can modify the Active Directory schema structure and backdoor any to-be-created Group/GPO by adding a compromised account to the default object ACL.|
|DNS Admins|Members can load a DLL on a DC, but do not have the necessary permissions to restart the DNS server. They can load a malicious DLL and wait for a reboot as a persistence mechanism. Loading a DLL will often result in the service crashing. A more reliable way to exploit this group is to [create a WPAD record](https://web.archive.org/web/20231115070425/https://cube0x0.github.io/Pocing-Beyond-DA/).|

#### User Rights




### Renabled disabled permissions
``` powershell
Function Enable-Privilege {  
    <#  
        .SYNOPSIS  
            Enables specific privilege or privileges on the current process.  
  
        .DESCRIPTION  
            Enables specific privilege or privileges on the current process.  
          
        .PARAMETER Privilege  
            Specific privilege/s to enable on the current process  
          
        .NOTES  
            Name: Enable-Privilege  
            Author: Boe Prox  
            Version History:  
                1.0 - Initial Version  
  
        .EXAMPLE  
        Enable-Privilege -Privilege SeBackupPrivilege  
  
        Description  
        -----------  
        Enables the SeBackupPrivilege on the existing process  
  
        .EXAMPLE  
        Enable-Privilege -Privilege SeBackupPrivilege, SeRestorePrivilege, SeTakeOwnershipPrivilege  
  
        Description  
        -----------  
        Enables the SeBackupPrivilege, SeRestorePrivilege and SeTakeOwnershipPrivilege on the existing process  
          
    #>  
    [cmdletbinding(  
        SupportsShouldProcess = $True  
    )]  
    Param (  
        [parameter(Mandatory = $True)]  
        [Privileges[]]$Privilege  
    )      
    If ($PSCmdlet.ShouldProcess("Process ID: $PID", "Enable Privilege(s): $($Privilege -join ', ')")) {  
        #region Constants  
        $SE_PRIVILEGE_ENABLED = 0x00000002  
        $SE_PRIVILEGE_DISABLED = 0x00000000  
        $TOKEN_QUERY = 0x00000008  
        $TOKEN_ADJUST_PRIVILEGES = 0x00000020  
        #endregion Constants  
  
        $TokenPriv = New-Object TokPriv1Luid  
        $HandleToken = [intptr]::Zero  
        $TokenPriv.Count = 1  
        $TokenPriv.Attr = $SE_PRIVILEGE_ENABLED  
      
        #Open the process token  
        $Return = [PoshPrivilege]::OpenProcessToken(  
            [PoshPrivilege]::GetCurrentProcess(),  
            ($TOKEN_QUERY -BOR $TOKEN_ADJUST_PRIVILEGES),   
            [ref]$HandleToken  
        )      
        If (-NOT $Return) {  
            Write-Warning "Unable to open process token! Aborting!"  
            Break  
        }  
        ForEach ($Priv in $Privilege) {  
            $PrivValue = $Null  
            $TokenPriv.Luid = 0  
            #Lookup privilege value  
            $Return = [PoshPrivilege]::LookupPrivilegeValue($Null, $Priv, [ref]$PrivValue)               
            If ($Return) {  
                $TokenPriv.Luid = $PrivValue  
                #Adjust the process privilege value  
                $return = [PoshPrivilege]::AdjustTokenPrivileges(  
                    $HandleToken,   
                    $False,   
                    [ref]$TokenPriv,   
                    [System.Runtime.InteropServices.Marshal]::SizeOf($TokenPriv),   
                    [IntPtr]::Zero,   
                    [IntPtr]::Zero  
                )  
                If (-NOT $Return) {  
                    Write-Warning "Unable to enable privilege <$priv>! "  
                }  
            }  
        }  
    }  
}



```


### adjust token privs
```powershell
param(    ## The privilege to adjust. This set is taken from
    ## http://msdn.microsoft.com/en-us/library/bb530716(VS.85).aspx

    [ValidateSet(
        "SeAssignPrimaryTokenPrivilege", "SeAuditPrivilege", "SeBackupPrivilege",
        "SeChangeNotifyPrivilege", "SeCreateGlobalPrivilege", "SeCreatePagefilePrivilege",
        "SeCreatePermanentPrivilege", "SeCreateSymbolicLinkPrivilege", "SeCreateTokenPrivilege",
        "SeDebugPrivilege", "SeEnableDelegationPrivilege", "SeImpersonatePrivilege", "SeIncreaseBasePriorityPrivilege",
        "SeIncreaseQuotaPrivilege", "SeIncreaseWorkingSetPrivilege", "SeLoadDriverPrivilege",
        "SeLockMemoryPrivilege", "SeMachineAccountPrivilege", "SeManageVolumePrivilege",
        "SeProfileSingleProcessPrivilege", "SeRelabelPrivilege", "SeRemoteShutdownPrivilege",
        "SeRestorePrivilege", "SeSecurityPrivilege", "SeShutdownPrivilege", "SeSyncAgentPrivilege",
        "SeSystemEnvironmentPrivilege", "SeSystemProfilePrivilege", "SeSystemtimePrivilege",
        "SeTakeOwnershipPrivilege", "SeTcbPrivilege", "SeTimeZonePrivilege", "SeTrustedCredManAccessPrivilege",
        "SeUndockPrivilege", "SeUnsolicitedInputPrivilege")]
    $Privilege,

    ## The process on which to adjust the privilege. Defaults to the current process.
    $ProcessId = $pid,

    ## Switch to disable the privilege, rather than enable it.
    [Switch] $Disable
)

## Taken from P/Invoke.NET with minor adjustments.
$definition = @'
using System;

using System.Runtime.InteropServices;
public class AdjPriv
{
    [DllImport("advapi32.dll", ExactSpelling = true, SetLastError = true)]
    internal static extern bool AdjustTokenPrivileges(IntPtr htok, bool disall,
        ref TokPriv1Luid newst, int len, IntPtr prev, IntPtr relen);

    [DllImport("advapi32.dll", ExactSpelling = true, SetLastError = true)]
    internal static extern bool OpenProcessToken(IntPtr h, int acc, ref IntPtr phtok);

    [DllImport("advapi32.dll", SetLastError = true)]
    internal static extern bool LookupPrivilegeValue(string host, string name, ref long pluid);

    [StructLayout(LayoutKind.Sequential, Pack = 1)]
    internal struct TokPriv1Luid
    {
        public int Count;
        public long Luid;
        public int Attr;
    }

    internal const int SE_PRIVILEGE_ENABLED = 0x00000002;
    internal const int SE_PRIVILEGE_DISABLED = 0x00000000;
    internal const int TOKEN_QUERY = 0x00000008;
    internal const int TOKEN_ADJUST_PRIVILEGES = 0x00000020;

    public static bool EnablePrivilege(long processHandle, string privilege, bool disable)
    {
        bool retVal;
        TokPriv1Luid tp;
        IntPtr hproc = new IntPtr(processHandle);
        IntPtr htok = IntPtr.Zero;
        retVal = OpenProcessToken(hproc, TOKEN_ADJUST_PRIVILEGES | TOKEN_QUERY, ref htok);
        tp.Count = 1;
        tp.Luid = 0;

        if(disable)
        {
            tp.Attr = SE_PRIVILEGE_DISABLED;
        }
        else
        {
            tp.Attr = SE_PRIVILEGE_ENABLED;
        }

        retVal = LookupPrivilegeValue(null, privilege, ref tp.Luid);
        retVal = AdjustTokenPrivileges(htok, false, ref tp, 0, IntPtr.Zero, IntPtr.Zero);
        return retVal;
    }
}
'@

$processHandle = (Get-Process -id $ProcessId).Handle
$type = Add-Type $definition -PassThru
$type[0]::EnablePrivilege($processHandle, $Privilege, $Disable)
```


### Find rights
```
whoami /priv
```


