Windows ttl=125

# rustscan

```
PORT      STATE SERVICE      REASON
21/tcp    open  ftp          syn-ack ttl 125
80/tcp    open  http         syn-ack ttl 125
135/tcp   open  msrpc        syn-ack ttl 125
139/tcp   open  netbios-ssn  syn-ack ttl 125
445/tcp   open  microsoft-ds syn-ack ttl 125
5040/tcp  open  unknown      syn-ack ttl 125
7680/tcp  open  pando-pub    syn-ack ttl 125
9998/tcp  open  distinct32   syn-ack ttl 125
49664/tcp open  unknown      syn-ack ttl 125
49665/tcp open  unknown      syn-ack ttl 125
49666/tcp open  unknown      syn-ack ttl 125
49667/tcp open  unknown      syn-ack ttl 125
49668/tcp open  unknown      syn-ack ttl 125
49669/tcp open  unknown      syn-ack ttl 125
```

# initial/priv esc

Everything was decent on each port but 9998 stands out

![[Pasted image 20240618014534.png]]

https://github.com/devzspy/CVE-2019-7214/blob/master/cve-2019-7214.py

quick win

![[Pasted image 20240618014617.png]]

.NET runs on 17001 that's why