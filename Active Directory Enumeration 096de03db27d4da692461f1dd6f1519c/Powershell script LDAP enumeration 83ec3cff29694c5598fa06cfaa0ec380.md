# Powershell script LDAP enumeration

```markdown
echo $PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name > enumeration.ps1
echo $DN = ([adsi]'').distinguishedName >> enumeration.ps1
echo $LDAP = "LDAP://$PDC/$DN" >> enumeration.ps1
echo $LDAP >> enumeration.ps1

**Bypass powershell security-**
powershell -ep bypass

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

# Usage example:
# Import the function and run a search
Import-Module .\1.ps1

# To search for all user accounts in the domain
LDAPSearch -LDAPQuery "(samAccountType=805306368)"

# To search directly for an Object Class, which is a component of AD that defines the object type
LDAPSearch -LDAPQuery "(objectclass=group)"

# To enumerate specific groups and their members
$sales = LDAPSearch -LDAPQuery "(&(objectCategory=group)(cn=Sales Department))"
$sales.properties.member

$group = LDAPSearch -LDAPQuery "(&(objectCategory=group)(cn=Development Department*))"
$group.properties.member

$group = LDAPSearch -LDAPQuery "(&(objectCategory=group)(cn=Management Department*))"
$group.properties.member

```