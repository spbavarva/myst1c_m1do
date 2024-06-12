**Useful Links**

https://github.com/rodolfomarianocy/OSCP-Tricks-2023/blob/main/active_directory.md

https://github.com/brianlam38/OSCP-2022/blob/main/cheatsheet-active-directory.md


Connect to dc with xfreerdp
```
xfreerdp /v:$IP /u:user /p:'pass' /d:OSCP.exam  /cert-ignore /timeout:60000
```
# with net.exe

### /domain

```
net user

net user /domain
```

with 1st command we can see all listed user on this particular PC not who are registered in domain

But with 2nd command, we can see all user on that domain

![[Pasted image 20240515143748.png]]

#### we can see which users are in which groups

```
net user $username /domain
```

![[Pasted image 20240515144110.png]]

check for all users and see their groups and focus where we will get more useful info. **If not all, at least suspicious users!**

If user is part of *Domain Admins* group and we can take over that, which means we Pawn3d AD!

#### check all groups

```
net group /domain
```

we can check all groups present in domain

![[Pasted image 20240515145138.png]]

We can filter which groups are uncommon

#### inspect particular group

```
net group "group_name" /domain
```

![[Pasted image 20240515145245.png]]


# LDAP

**LDAP is not exclusive to AD!**

AD enumeration relies on LDAP. So, when we query user or search objects, like printer, LDAP used for communication channel for query

### LDAP path format
```
LDAP://HostName[:PortNumber][/DistinguishedName]
```

Hostname = computer name/IP/domain
Portnumber = optional, but can specify if we come across a domain in the future using non-default ports
DistinguishedName (DN) = a name that uniquely identifies an object in AD

We should always check with updated information. For that PDC (Primary DC) is available, which is only 1 in AD. To find the PDC, we need to find the DC holding the *PdcRoleOwner* property

#### Example of DN
```
CN=Stephanie,CN=Users,DC=corp,DC=com
```

CN = common name
DC = Domain component

read from right to left for hierarchy

#### building script for **LDAP**

```
[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
```
above line will give PDC info

![[Pasted image 20240515155013.png]]

**SCRIPT**
```
$PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name
$DN = ([adsi]'').distinguishedName 
$LDAP = "LDAP://$PDC/$DN"
$LDAP
```

![[Pasted image 20240515155613.png]]

**Updated enumeration.ps1**
```
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
$PDC = $domainObj.PdcRoleOwner.Name
$DN = ([adsi]'').distinguishedName 
$LDAP = "LDAP://$PDC/$DN"

$direntry = New-Object System.DirectoryServices.DirectoryEntry($LDAP)

$dirsearcher = New-Object System.DirectoryServices.DirectorySearcher($direntry)
$dirsearcher.filter="samAccountType=805306368"
$result = $dirsearcher.FindAll()

Foreach($obj in $result)
{
    Foreach($prop in $obj.Properties)
    {
        $prop
    }

    Write-Host "-------------------------------"
}
```

**function.ps1**
```
function LDAPSearch {
    param (
        [string]$LDAPQuery
    )

    $PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name
    $DistinguishedName = ([adsi]'').distinguishedName

    $DirectoryEntry = New-Object System.DirectoryServices.DirectoryEntry("LDAP://$PDC/$DistinguishedName")

    $DirectorySearcher = New-Object System.DirectoryServices.DirectorySearcher($DirectoryEntry, $LDAPQuery)

    return $DirectorySearcher.FindAll()

}
```

commands
```
Import-Module .\function.ps1

LDAPSearch -LDAPQuery "(samAccountType=805306368)"

LDAPSearch -LDAPQuery "(objectclass=group)"
```


# powerview

we can get all things which we got by above two ways with this module

transfer this exe and import it

```
Import-Module .\PowerView.ps1
```

### Domain info

```
Get-NetDomain
```

![[Pasted image 20240515175602.png]]

### info about users

```
Get-NetUser

Get-NetUser | select cn

Get-NetUser "username"

Get-NetUser | select cn,pwdlastset,lastlogon
```

![[Pasted image 20240515175524.png]]

![[Pasted image 20240515175454.png]]

### info about groups

```
Get-NetGroup | select cn

Get-NetGroup "Sales Department" | select member
```

![[Pasted image 20240515175908.png]]

### info about OS

```
Get-NetComputer

Get-NetComputer | select operatingsystem,dnshostname,distinguishedname,operatingsystemversion
```

![[Pasted image 20240515182111.png]]

# PsLoggedon.exe

### Enumerate all active sessions
```
Get-NetSession -ComputerName dc1
```

### check logged in users

```
Get-NetComputer | select dnshostname,operatingsystem,operatingsystemversion
```

get CN of machines and check what users are logged on in which machine

```
.\PsLoggedon.exe \\files04
```


# SPN

### impacket

we can enum SPN with impacket but we need to give dc IP always
```
impacket-GetUserSPNs -dc-ip 192.168.179.70 corp.com/stephanie
```

![[Pasted image 20240516005847.png]]

### GetUsersSPNs.ps1

https://github.com/nidem/kerberoast/blob/master/GetUserSPNs.ps1

![[Pasted image 20240516010316.png]]

### Powerview

```
Get-NetUser -SPN | select samaccountname,serviceprincipalname

nslookup.exe web04.corp.com
nslookup.exe [spn-name]
```

![[Pasted image 20240516010545.png]]


# ACE-ACL AD permission

### AD permission types

> **GenericAll**: Full permissions on object
> **GenericWrite**: Edit certain attributes on the object
> **WriteOwner**: Change ownership of the object
> **WriteDACL**: Edit ACE's applied to object
> **AllExtendedRights**: Change password, reset password, etc.
> **ForceChangePassword**: Password change for object
> **Self (Self-Membership)**: Add ourselves to for example a group

We are interested to find user with GenericAll permission as we can then add that user to that group or something

of course with **powerview.ps1**

```
Get-ObjectAcl -Identity stephanie
```

![[Pasted image 20240516013553.png]]

Output is too much!!

### Converting the ObjectISD into name

```
Convert-SidToName S-1-5-21-1987370270-658905905-1781884369-553

Convert-SidToName [SID]
```

![[Pasted image 20240516013746.png]]

### get all SID of genericall to specific group

```
Get-ObjectAcl -Identity "Management Department" | ? {$_.ActiveDirectoryRights -eq "GenericAll"} | select SecurityIdentifier,ActiveDirectoryRights
```

![[Pasted image 20240516013923.png]]

```
"S-1-5-21-1987370270-658905905-1781884369-512","S-1-5-21-1987370270-658905905-1781884369-1104","S-1-5-32-548","S-1-5-18","S-1-5-21-1987370270-658905905-1781884369-519" | Convert-SidToName
```

![[Pasted image 20240516013941.png]]

### add user to that group where user have genericall perm

```
net group "Management Department" stephanie /add /domain

Get-NetGroup "Management Department" | select member
```

![[Pasted image 20240516014016.png]]


# Shares

with powerview

```
Find-DomainShare
```

![[Pasted image 20240516023110.png]]

```
ls \\dc1.corp.com\sysvol\corp.com\

ls \\dc1.corp.com\sysvol\corp.com\Policies\

cat \\dc1.corp.com\sysvol\corp.com\Policies\oldpolicy\old-policy-backup.xml
```

![[Pasted image 20240516023123.png]]

Normally passwords are encrypted eith AES-256, we can decrypt it with **gpp-decrypt**

cat \\dc1.corp.com\sysvol\corp.com\Policies\oldpolicy\old-policy-backup.xml

![[Pasted image 20240516023135.png]]


# Bloodhound

### Sharphound

```
Import-Module .\Sharphound.ps1

Invoke-BloodHound
```

![[Pasted image 20240516023712.png]]

```
Invoke-BloodHound -CollectionMethod All
```



transfer zip to kali

### bloodohound

```
bloodhound
```