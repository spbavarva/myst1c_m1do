```
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 61
80/tcp open  http    syn-ack ttl 61
```

## nmap
```
sudo nmap -sS -Pn -T4 -sCV -O -A -p22,80 $IP2 -oA websrv1/full.nmap      
```

```
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-05-22 07:12 EDT
Nmap scan report for 192.168.241.244
Host is up (0.081s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 4f:c8:5e:cd:62:a0:78:b4:6e:d8:dd:0e:0b:8b:3a:4c (ECDSA)
|_  256 8d:6d:ff:a4:98:57:82:95:32:82:64:53:b2:d7:be:44 (ED25519)
80/tcp open  http    Apache httpd 2.4.52 ((Ubuntu))
| http-title: BEYOND Finances &#8211; We provide financial freedom
|_Requested resource was http://192.168.241.244/main/
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-generator: WordPress 6.0.2
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 2.6.18 (87%), Linux 4.15 - 5.8 (87%), Linux 5.0 (87%), Linux 5.0 - 5.4 (87%), Linux 2.6.32 (87%), Linux 3.4 (87%), Linux 3.5 (87%), Linux 3.7 (87%), Synology DiskStation Manager 5.1 (87%), Linux 5.3 - 5.4 (87%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 4 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 80/tcp)
HOP RTT      ADDRESS
1   89.72 ms 192.168.45.1
2   89.69 ms 192.168.45.254
3   89.74 ms 192.168.251.1
4   89.80 ms 192.168.241.244

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 17.77 seconds

```