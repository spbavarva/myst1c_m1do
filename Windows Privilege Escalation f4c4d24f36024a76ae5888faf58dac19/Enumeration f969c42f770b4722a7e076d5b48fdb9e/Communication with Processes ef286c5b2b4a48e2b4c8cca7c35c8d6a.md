# Communication with Processes

### More on Named Pipes

Pipes are used for communication between two applications or processes using shared memory. There are two types of pipes, [named pipes](https://docs.microsoft.com/en-us/windows/win32/ipc/named-pipes) and anonymous pipes. An example of a named pipe is `\\.\PipeName\\ExampleNamedPipeServer`.
 Windows systems use a client-server implementation for pipe 
communication. In this type of implementation, the process that creates a
 named pipe is the server, and the process communicating with the named 
pipe is the client. Named pipes can communicate using `half-duplex`, or a one-way channel with the client only being able to write data to the server, or `duplex`,
 which is a two-way communication channel that allows the client to 
write data over the pipe, and the server to respond back with data over 
that pipe. Every active connection to a named pipe server results in the
 creation of a new named pipe. These all share the same pipe name but 
communicate using a different data buffer.

We can use the tool [PipeList](https://docs.microsoft.com/en-us/sysinternals/downloads/pipelist) from the Sysinternals Suite to enumerate instances of named pipes.

### Listing Named Pipes with Pipelist

Communication with Processes

```
C:\htb> pipelist.exe /accepteula

PipeList v1.02 - Lists open named pipes
Copyright (C) 2005-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

Pipe Name                                    Instances       Max Instances
---------                                    ---------       -------------
InitShutdown                                      3               -1
lsass                                             4               -1
ntsvcs                                            3               -1
scerpc                                            3               -1
Winsock2\CatalogChangeListener-340-0              1                1
Winsock2\CatalogChangeListener-414-0              1                1
epmapper                                          3               -1
Winsock2\CatalogChangeListener-3ec-0              1                1
Winsock2\CatalogChangeListener-44c-0              1                1
LSM_API_service                                   3               -1
atsvc                                             3               -1
Winsock2\CatalogChangeListener-5e0-0              1                1
eventlog                                          3               -1
Winsock2\CatalogChangeListener-6a8-0              1                1
spoolss                                           3               -1
Winsock2\CatalogChangeListener-ec0-0              1                1
wkssvc                                            4               -1
trkwks                                            3               -1
vmware-usbarbpipe                                 5               -1
srvsvc                                            4               -1
ROUTER                                            3               -1
vmware-authdpipe                                  1                1

<SNIP>

```

Additionally, we can use PowerShell to list named pipes using `gci` (`Get-ChildItem`).

### Listing Named Pipes with PowerShell

Communication with Processes

```powershell
PS C:\htb>  gci \\.\pipe\

    Directory: \\.\pipe

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
------       12/31/1600   4:00 PM              3 InitShutdown
------       12/31/1600   4:00 PM              4 lsass
------       12/31/1600   4:00 PM              3 ntsvcs
------       12/31/1600   4:00 PM              3 scerpc

    Directory: \\.\pipe\Winsock2

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
------       12/31/1600   4:00 PM              1 Winsock2\CatalogChangeListener-34c-0

    Directory: \\.\pipe

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
------       12/31/1600   4:00 PM              3 epmapper

<SNIP>

```

After obtaining a listing of named pipes, we can use [Accesschk](https://docs.microsoft.com/en-us/sysinternals/downloads/accesschk)
 to enumerate the permissions assigned to a specific named pipe by 
reviewing the Discretionary Access List (DACL), which shows us who has 
the permissions to modify, write, read, or execute a resource. Let's 
take a look at the `LSASS` process. We can also review the DACLs of all named pipes using the command  `.\accesschk.exe /accepteula \pipe\`.

### Reviewing LSASS Named Pipe Permissions

Communication with Processes

```
C:\htb> accesschk.exe /accepteula \\.\Pipe\lsass -v

Accesschk v6.12 - Reports effective permissions for securable objects
Copyright (C) 2006-2017 Mark Russinovich
Sysinternals - www.sysinternals.com

\\.\Pipe\lsass
  Untrusted Mandatory Level [No-Write-Up]
  RW Everyone
        FILE_READ_ATTRIBUTES
        FILE_READ_DATA
        FILE_READ_EA
        FILE_WRITE_ATTRIBUTES
        FILE_WRITE_DATA
        FILE_WRITE_EA
        SYNCHRONIZE
        READ_CONTROL
  RW NT AUTHORITY\ANONYMOUS LOGON
        FILE_READ_ATTRIBUTES
        FILE_READ_DATA
        FILE_READ_EA
        FILE_WRITE_ATTRIBUTES
        FILE_WRITE_DATA
        FILE_WRITE_EA
        SYNCHRONIZE
        READ_CONTROL
  RW APPLICATION PACKAGE AUTHORITY\Your Windows credentials
        FILE_READ_ATTRIBUTES
        FILE_READ_DATA
        FILE_READ_EA
        FILE_WRITE_ATTRIBUTES
        FILE_WRITE_DATA
        FILE_WRITE_EA
        SYNCHRONIZE
        READ_CONTROL
  RW BUILTIN\Administrators
        FILE_ALL_ACCESS

```

From the output above, we can see that only administrators have full access to the LSASS process, as expected.

---

## Named Pipes Attack Example

Let's walk through an example of taking advantage of an exposed named pipe to escalate privileges. This [WindscribeService Named Pipe Privilege Escalation](https://www.exploit-db.com/exploits/48021) is a great example. Using `accesschk` we can search for all named pipes that allow write access with a command such as `accesschk.exe -w \pipe\* -v` and notice that the `WindscribeService` named pipe allows `READ` and `WRITE` access to the `Everyone` group, meaning all authenticated users.

### Checking WindscribeService Named Pipe Permissions

Confirming with `accesschk` we see that the Everyone group does indeed have `FILE_ALL_ACCESS` (All possible access rights) over the pipe.

Communication with Processes

```
C:\htb> accesschk.exe -accepteula -w \pipe\WindscribeService -v

Accesschk v6.13 - Reports effective permissions for securable objects
Copyright ⌐ 2006-2020 Mark Russinovich
Sysinternals - www.sysinternals.com

\\.\Pipe\WindscribeService
  Medium Mandatory Level (Default) [No-Write-Up]
  RW Everyone
        FILE_ALL_ACCESS

```