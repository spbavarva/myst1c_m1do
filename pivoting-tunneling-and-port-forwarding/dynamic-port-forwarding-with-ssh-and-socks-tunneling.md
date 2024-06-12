
# SSH Local Port Forwarding

this technique is used when we know which service is running
![[image 20.png]]

![[image 21.png]]

in this case it is `mysql`

```bash
ssh -L 1234:localhost:3306 ubuntu@10.129.202.64
```

we must know ssh password also

![[image 22.png]]
SUCCESS

### Confirming Port Forward with Netstat

```bash
netstat -antp | grep 1234
```

![[image 23.png]]

### Confirming Port Forward with Nmap

```bash
nmap -v -sV -p1234 localhost
```

![[image 24.png]]

## Forward Multiple Ports

```bash
ssh -L 1234:localhost:3306 -L 8080:localhost:80 ubuntu@10.129.202.64
```

# SSH tunneling over SOCKS proxy (PIVOT)

when we don't know other PC IP and the service then this technique should use


> [!NOTE]
> SOCKS proxies are currently of two types: SOCKS4 and SOCKS5. SOCKS4 doesn't provide any authentication and UDP support, whereas SOCKS5 does provide that

![[image 25.png]]

### Do dynamic port forwarding with SSH

```bash
ssh -D 9050 ubuntu@10.129.202.64
    (proxychain port) (victim IP)
```

### checking /etc/proxychains.conf

![[image 26.png]]

#### run ping sweep to get active host

```bash
proxychains nmap -v -sn 172.16.5.1-200
                        (get this ip by victim machine and find other NIC)
```

![[image 27.png]]

![[image 28.png]]


### Now we can do nmap scan using proxychains

![[image 29.png]]

#### use commands with proxychains

```bash
proxychains xfreerdp /v:172.16.5.19 /u:victor /p:pass@123 /cert-ignore /timeout:60000
```
