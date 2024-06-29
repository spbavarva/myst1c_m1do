Linux ttl=61

# rustscan

```
PORT     STATE SERVICE         REASON
22/tcp   open  ssh             syn-ack ttl 61
139/tcp  open  netbios-ssn     syn-ack ttl 61
445/tcp  open  microsoft-ds    syn-ack ttl 61
631/tcp  open  ipp             syn-ack ttl 61
8080/tcp open  http-proxy      syn-ack ttl 61
8081/tcp open  blackice-icecap syn-ack ttl 61
```

# nmap

```
PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
139/tcp   open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp   open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
631/tcp   open  ipp         CUPS 2.2
2181/tcp  open  zookeeper   Zookeeper 3.4.6-1569965 (Built on 02/20/2014)
2222/tcp  open  ssh         OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
8080/tcp  open  http        Jetty 1.0
8081/tcp  open  http        nginx 1.14.2
34631/tcp open  java-rmi    Java RMI
Service Info: Host: PELICAN; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

```
PORT      STATE  SERVICE     VERSION
22/tcp    open   ssh         OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 a8:e1:60:68:be:f5:8e:70:70:54:b4:27:ee:9a:7e:7f (RSA)
|   256 bb:99:9a:45:3f:35:0b:b3:49:e6:cf:11:49:87:8d:94 (ECDSA)
|_  256 f2:eb:fc:45:d7:e9:80:77:66:a3:93:53:de:00:57:9c (ED25519)
139/tcp   open   netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp   open   netbios-ssn Samba smbd 4.9.5-Debian (workgroup: WORKGROUP)
631/tcp   open   ipp         CUPS 2.2
|_http-title: Forbidden - CUPS v2.2.10
| http-methods: 
|_  Potentially risky methods: PUT
|_http-server-header: CUPS/2.2 IPP/2.1
2181/tcp  open   zookeeper   Zookeeper 3.4.6-1569965 (Built on 02/20/2014)
2222/tcp  open   ssh         OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 a8:e1:60:68:be:f5:8e:70:70:54:b4:27:ee:9a:7e:7f (RSA)
|   256 bb:99:9a:45:3f:35:0b:b3:49:e6:cf:11:49:87:8d:94 (ECDSA)
|_  256 f2:eb:fc:45:d7:e9:80:77:66:a3:93:53:de:00:57:9c (ED25519)
8080/tcp  open   http        Jetty 1.0
|_http-title: Error 404 Not Found
|_http-server-header: Jetty(1.0)
8081/tcp  open   http        nginx 1.14.2
|_http-title: Did not follow redirect to http://192.168.225.98:8080/exhibitor/v1/ui/index.html
|_http-server-header: nginx/1.14.2
34636/tcp closed unknown
```

# initial

smbmap, ftp, gobuster with any ports didn't worked and look at `8080` and seen `zookeeper` in nmap results

https://www.exploit-db.com/exploits/48654

did same steps as exploit tells

![[Pasted image 20240618123428.png]]

got www-data shell

# priv esc

![[Pasted image 20240618123543.png]]

`gcore` set with sudo permission and it need PID

### SUID

![[Pasted image 20240618123652.png]]

got SUID and now if I can find it's PID and give it to `gcore` then it will run as **root**

![[Pasted image 20240618123825.png]]

but my syntax is little not good

![[Pasted image 20240618123938.png]]

found pass for root with `strings`

![[Pasted image 20240618124047.png]]

