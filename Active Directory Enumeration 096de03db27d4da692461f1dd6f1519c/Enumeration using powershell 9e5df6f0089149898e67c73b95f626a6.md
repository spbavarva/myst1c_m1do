# Enumeration using powershell

### Import powerview and run following to enumerate

```markdown
*numeImport-Module .\PowerView.ps1

**Get Domain Information-**
Get-NetDomain

**Get List of all users-**
Get-NetUser

**Get more information about users-**
Get-NetUser | select cn,pwdlastset,lastlogon

**Enumerate groups information-**
Get-NetGroup | select cn

**Enumerate members of group-**
Get-NetGroup "Sales Department" | select member

**Enumerate operating systems-**
Get-NetComputer

**Hostnames Enumeration-**
Get-NetComputer | select operatingsystem,dnshostname

**Find domain admin-**
Find-LocalAdminAccess

**Enumerate logged in users-**
Get-NetSession -ComputerName files04 -Verbose
Get-NetSession -ComputerName client74

**To view the permissions for all users-**
Get-Acl -Path HKLM:SYSTEM\CurrentControlSet\Services\LanmanServer\DefaultSecurity\ | fl

**To enumerate all information about the operating systems-**
Get-NetComputer | select dnshostname,operatingsystem,operatingsystemversion

**Enumerate logged in users-**
.\PsLoggedon.exe \\files04

**Enumerate users using setspn**-
setspn -L iis_service

**Enumerate samaccountname**-
Get-NetUser -SPN | select samaccountname,serviceprincipalname

nslookup.exe web04.corp.com

**Get more information from a user-**
Get-DomainUser -Identity mmorgan -Domain inlanefreight.local | Select-Object -Property name,samaccountname,description,memberof,whencreated,pwdlastset,lastlogontimestamp,accountexpires,admincount,userprincipalname,serviceprincipalname,useraccountcontrol

**Recursive group membership-**
Get-DomainGroupMember -Identity "Domain Admins" -Recurse*

```

```markdown
Finding admin access that our user has on different machine-

Find-LocalAdminAccess

Test-AdminAccess -ComputerName ACADEMY-EA-MS01

```

Cheatsheet

| Command | Description |
| --- | --- |
| Export-PowerViewCSV | Append results to a CSV file |
| ConvertTo-SID | Convert a User or group name to its SID value |
| Get-DomainSPNTicket | Requests the Kerberos ticket for a specified Service Principal Name (SPN) account |
| Domain/LDAP Functions: |  |
| Get-Domain | Will return the AD object for the current (or specified) domain |
| Get-DomainController | Return a list of the Domain Controllers for the specified domain |
| Get-DomainUser | Will return all users or specific user objects in AD |
| Get-DomainComputer | Will return all computers or specific computer objects in AD |
| Get-DomainGroup | Will return all groups or specific group objects in AD |
| Get-DomainOU | Search for all or specific OU objects in AD |
| Find-InterestingDomainAcl | Finds object ACLs in the domain with modification rights set to non-built in objects |
| Get-DomainGroupMember | Will return the members of a specific domain group |
| Get-DomainFileServer | Returns a list of servers likely functioning as file servers |
| Get-DomainDFSShare | Returns a list of all distributed file systems for the current (or specified) domain |
| GPO Functions: |  |
| Get-DomainGPO | Will return all GPOs or specific GPO objects in AD |
| Get-DomainPolicy | Returns the default domain policy or the domain controller policy for the current domain |
| Computer Enumeration Functions: |  |
| Get-NetLocalGroup | Enumerates local groups on the local or a remote machine |
| Get-NetLocalGroupMember | Enumerates members of a specific local group |
| Get-NetShare | Returns open shares on the local (or a remote) machine |
| Get-NetSession | Will return session information for the local (or a remote) machine |
| Test-AdminAccess | Tests if the current user has administrative access to the local (or a remote) machine |
| Threaded 'Meta'-Functions: |  |
| Find-DomainUserLocation | Finds machines where specific users are logged in |
| Find-DomainShare | Finds reachable shares on domain machines |
| Find-InterestingDomainShareFile | Searches for files matching specific criteria on readable shares in the domain |
| Find-LocalAdminAccess | Find machines on the local domain where the current user has local administrator access |
| Domain Trust Functions: |  |
| Get-DomainTrust | Returns domain trusts for the current domain or a specified domain |
| Get-ForestTrust | Returns all forest trusts for the current forest or a specified forest |
| Get-DomainForeignUser | Enumerates users who are in groups outside of the user's domain |
| Get-DomainForeignGroupMember | Enumerates groups with users outside of the group's domain and returns each foreign member |
| Get-DomainTrustMapping | Will enumerate all trusts for the current domain and any others seen. |