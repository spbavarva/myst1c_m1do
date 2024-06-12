---
description: >-
  What might an IT admin be doing on a day-to-day basis & which of those tasks
  may require credentials?
---

# Credential Hunting in Windows

### Running Lazagne all

```powershell
.\LaZagne.exe all
```
![[images/image 16.png]]

### Using findstr


```cmd-session
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml *.git *.ps1 *.yml
```

### Additional Consideration

* Passwords in Group Policy in the SYSVOL share
* Passwords in scripts in the SYSVOL share
* Password in scripts on IT shares
* Passwords in web.config files on dev machines and IT shares
* unattend.xml
* Passwords in the AD user or computer description fields
* KeePass databases --> pull hash, crack and get loads of access.
* Found on user systems and shares
* Files such as pass.txt, passwords.docx, passwords.xlsx found on user systems, shares,
