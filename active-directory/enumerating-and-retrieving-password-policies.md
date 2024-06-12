
## From Linux

### With creds

```bash
crackmapexec smb $IP -u $Username -p $Password --pass-pol
```

### SMB Null

```bash
rpcclient -U "" -N $IP
```

Useful command in rpcclient

* `querydominfo`
* `getdompwinfo`
* `enumdomusers`

### enum4linux & enum4linux-ng

```bash
enum4linux-ng -P $IP -oA ilfreight #(for pass policy)
```

***

## Null From Windows

### check if we have perm

```
net use \\DC01\ipc$ "" /u:""
```

![[image 37.png]]

### with cmd

```
net accounts
```

![[image 38.png]]

***

## Enumerating the Password Policy - from Linux - LDAP Anonymous Bind

```bash
ldapsearch -h $IP -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "*" | grep -m 1 -B 10 pwdHistoryLength
```


***

## Using powerview

```powershell
import-module .\PowerView.ps1
```

```powershell
Get-DomainPolicy
```

PowerView gave us the same output as our net accounts command
