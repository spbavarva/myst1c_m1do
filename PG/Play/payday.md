Linux ttl=61

# rustscan

```
PORT    STATE SERVICE      REASON
22/tcp  open  ssh          syn-ack ttl 61
80/tcp  open  http         syn-ack ttl 61
110/tcp open  pop3         syn-ack ttl 61
139/tcp open  netbios-ssn  syn-ack ttl 61
143/tcp open  imap         syn-ack ttl 61
445/tcp open  microsoft-ds syn-ack ttl 61
993/tcp open  imaps        syn-ack ttl 61
995/tcp open  pop3s        syn-ack ttl 61
```

# nmap

```
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 4.6p1 Debian 5build1 (protocol 2.0)
| ssh-hostkey: 
|   1024 f3:6e:87:04:ea:2d:b3:60:ff:42:ad:26:67:17:94:d5 (DSA)
|_  2048 bb:03:ce:ed:13:f1:9a:9e:36:03:e2:af:ca:b2:35:04 (RSA)
80/tcp  open  http        Apache httpd 2.2.4 ((Ubuntu) PHP/5.2.3-1ubuntu6)
|_http-server-header: Apache/2.2.4 (Ubuntu) PHP/5.2.3-1ubuntu6
|_http-title: CS-Cart. Powerful PHP shopping cart software
110/tcp open  pop3        Dovecot pop3d
|_ssl-date: 2024-06-26T09:56:49+00:00; +6s from scanner time.
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_DES_192_EDE3_CBC_WITH_MD5
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|     SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
|     SSL2_RC4_128_WITH_MD5
|_    SSL2_RC2_128_CBC_WITH_MD5
|_pop3-capabilities: CAPA UIDL SASL RESP-CODES TOP STLS PIPELINING
| ssl-cert: Subject: commonName=ubuntu01/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX
| Not valid before: 2008-04-25T02:02:48
|_Not valid after:  2008-05-25T02:02:48
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: MSHOME)
143/tcp open  imap        Dovecot imapd
| ssl-cert: Subject: commonName=ubuntu01/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX
| Not valid before: 2008-04-25T02:02:48
|_Not valid after:  2008-05-25T02:02:48
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_DES_192_EDE3_CBC_WITH_MD5
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|     SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
|     SSL2_RC4_128_WITH_MD5
|_    SSL2_RC2_128_CBC_WITH_MD5
|_imap-capabilities: Capability completed SORT LITERAL+ UNSELECT MULTIAPPEND OK LOGIN-REFERRALS STARTTLS IDLE NAMESPACE THREAD=REFERENCES SASL-IR LOGINDISABLEDA0001 CHILDREN IMAP4rev1
|_ssl-date: 2024-06-26T09:56:49+00:00; +6s from scanner time.
445/tcp open  netbios-ssn Samba smbd 3.0.26a (workgroup: MSHOME)
993/tcp open  ssl/imap    Dovecot imapd
|_imap-capabilities: completed SORT AUTH=PLAINA0001 UNSELECT LITERAL+ OK LOGIN-REFERRALS Capability IDLE NAMESPACE THREAD=REFERENCES SASL-IR MULTIAPPEND CHILDREN IMAP4rev1
|_ssl-date: 2024-06-26T09:56:49+00:00; +6s from scanner time.
| ssl-cert: Subject: commonName=ubuntu01/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX
| Not valid before: 2008-04-25T02:02:48
|_Not valid after:  2008-05-25T02:02:48
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_DES_192_EDE3_CBC_WITH_MD5
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|     SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
|     SSL2_RC4_128_WITH_MD5
|_    SSL2_RC2_128_CBC_WITH_MD5
995/tcp open  ssl/pop3    Dovecot pop3d
|_ssl-date: 2024-06-26T09:56:49+00:00; +7s from scanner time.
| ssl-cert: Subject: commonName=ubuntu01/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX
| Not valid before: 2008-04-25T02:02:48
|_Not valid after:  2008-05-25T02:02:48
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_DES_192_EDE3_CBC_WITH_MD5
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|     SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
|     SSL2_RC4_128_WITH_MD5
|_    SSL2_RC2_128_CBC_WITH_MD5
|_pop3-capabilities: CAPA UIDL SASL(PLAIN) USER TOP RESP-CODES PIPELINING
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: printer|general purpose|remote management|WAP|switch|router|specialized|print server
Running (JUST GUESSING): Kyocera embedded (92%), Linux 2.6.X|2.4.X (91%), Dell embedded (90%), Extreme Networks ExtremeXOS 12.X|15.X (90%), MikroTik RouterOS 6.X (90%), AVM embedded (89%), Google embedded (89%), HP embedded (89%)
OS CPE: cpe:/h:kyocera:cs-2560 cpe:/o:linux:linux_kernel:2.6.22 cpe:/h:dell:remote_access_card:6 cpe:/o:linux:linux_kernel:2.4.20 cpe:/o:extremenetworks:extremexos:12.5.4 cpe:/o:mikrotik:routeros:6.15 cpe:/h:avm:fritz%21box_fon_wlan_7170 cpe:/o:extremenetworks:extremexos:15.3
Aggressive OS guesses: Kyocera CopyStar CS-2560 printer (92%), Linux 2.6.22 (91%), Dell Remote Access Controller (DRAC 6) (90%), Tomato 1.27 - 1.28 (Linux 2.4.20) (90%), Dell Integrated Remote Access Controller (iDRAC) (90%), Extreme Networks ExtremeXOS 12.5.4 (90%), DD-WRT v24-presp2 (Linux 2.6.34) (90%), MikroTik RouterOS 6.15 (Linux 3.3.5) (90%), AVM FRITZ!Box FON WLAN 7170 WAP (89%), Dell Integrated Remote Access Controller (iDRAC9) (89%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 4 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.26a)
|   Computer name: payday
|   NetBIOS computer name: 
|   Domain name: 
|   FQDN: payday
|_  System time: 2024-06-26T05:56:40-04:00
|_clock-skew: mean: 40m06s, deviation: 1h38m00s, median: 5s
|_smb2-time: Protocol negotiation failed (SMB2)
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)

TRACEROUTE (using port 995/tcp)
HOP RTT      ADDRESS
1   91.19 ms 192.168.45.1
2   91.14 ms 192.168.45.254
3   91.23 ms 192.168.251.1
4   91.28 ms 192.168.157.39
```

# initial

did run dirbuster and get that it's some kind of CMS and there's `admin.php`

login with weak creds `admin:admin`

![[Pasted image 20240626160238.png]]

https://gist.github.com/momenbasel/ccb91523f86714edb96c871d4cf1d05c?permalink_comment_id=3608079#gistcomment-3608079

followed this discussion and got shell!

### rabbit holes

tried so many thing and machine is way more old like kernel is exploitable with `dirtycow` but `gcc` was not installed

# priv esc

looked at walkthrough and seen that everyone did bruteforce for `patrick`

how the hell anyone get that we need to bruteforce shit!

ssh into `patrick:patrick`

then simple he has `sudo (ALL)`