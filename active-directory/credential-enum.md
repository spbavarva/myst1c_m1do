# Credential enum

## From Linux

* \-u Username The user whose credentials we will use to authenticate
* \-p Password User's password
* Target (IP or FQDN) Target host to enumerate (in our case, the Domain Controller)
* \--users Specifies to enumerate Domain Users
* \--groups Specifies to enumerate domain groups
* \--loggedon-users Attempts to enumerate what users are logged on to a target, if any

we need `sudo` for CME

### CME - Domain User/Group/Logged on Users Enumeration

```bash
sudo crackmapexec smb $IP -u forend -p Klmcargo2 --users
```

```bash
sudo crackmapexec smb $IP -u forend -p Klmcargo2 --groups
```

```bash
sudo crackmapexec smb $IP -u forend -p Klmcargo2 --loggedon-users
```

### Share enum

```bash
sudo crackmapexec smb $IP -u forend -p Klmcargo2 --shares
```

#### Spider\_plus


```bash
sudo crackmapexec smb $IP -u forend -p Klmcargo2 -M spider_plus --share 'Department Shares'
```


/tmp/cme\_spider\_plus/ -> where output stares

### SMBmap

```bash
smbmap -u forend -p Klmcargo2 -d INLANEFREIGHT.LOCAL -H $IP
```

```bash
smbmap -u forend -p Klmcargo2 -d INLANEFREIGHT.LOCAL -H $IP -R 'Department Shares' --dir-only
```

### rpcclient

```bash
rpcclient -U "" -N $IP
```
