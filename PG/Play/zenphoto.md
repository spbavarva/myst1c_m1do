Linux ttl=61

# rustscan

```
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 61
23/tcp open  telnet  syn-ack ttl 61
80/tcp open  http    syn-ack ttl 61
```

# nmap

```
ORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 5.3p1 Debian 3ubuntu7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 83:92:ab:f2:b7:6e:27:08:7b:a9:b8:72:32:8c:cc:29 (DSA)
|_  2048 65:77:fa:50:fd:4d:9e:f1:67:e5:cc:0c:c6:96:f2:3e (RSA)
23/tcp   open  ipp     CUPS 1.4
|_http-title: 403 Forbidden
|_http-server-header: CUPS/1.4
| http-methods: 
|_  Potentially risky methods: PUT
80/tcp   open  http    Apache httpd 2.2.14 ((Ubuntu))
|_http-server-header: Apache/2.2.14 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
3306/tcp open  mysql   MySQL (unauthorized)
```

# rabbit hole

webpage was empty, so thought `CUPS` is way to go and find some CVEs but no luck

![[Pasted image 20240628013359.png]]

# initial

![[Pasted image 20240628013436.png]]

then got this directory with gobuster and checked `zenphoto` version and got exploit

https://www.exploit-db.com/exploits/18083

![[Pasted image 20240628013632.png]]

got shell!!

# priv esc

got pkexec in SUID and leapeas suggested that it's vulnerable to pwnkit

https://github.com/berdav/CVE-2021-4034

```
make
```

```
./cve-2021-4034
```

![[Pasted image 20240628014107.png]]

### intended way

kernel `2.6.32-21-generic`