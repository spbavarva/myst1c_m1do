Linux ttl=61

# rustscan

```
PORT    STATE SERVICE      REASON
22/tcp  open  ssh          syn-ack ttl 61
25/tcp  open  smtp         syn-ack ttl 61
80/tcp  open  http         syn-ack ttl 61
139/tcp open  netbios-ssn  syn-ack ttl 61
199/tcp open  smux         syn-ack ttl 61
445/tcp open  microsoft-ds syn-ack ttl 61
```

# nmap

```
PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         OpenSSH 3.8.1p1 Debian 8.sarge.6 (protocol 2.0)
| ssh-hostkey: 
|   1024 30:3e:a4:13:5f:9a:32:c0:8e:46:eb:26:b3:5e:ee:6d (DSA)
|_  1024 af:a2:49:3e:d8:f2:26:12:4a:a0:b5:ee:62:76:b0:18 (RSA)
25/tcp    open  smtp        Sendmail 8.13.4
| smtp-commands: localhost.localdomain Hello [192.168.45.170], pleased to meet you, ENHANCEDSTATUSCODES, PIPELINING, EXPN, VERB, 8BITMIME, SIZE, DSN, ETRN, DELIVERBY, HELP
|_ 2.0.0 This is sendmail version 8.13.4 2.0.0 Topics: 2.0.0 HELO EHLO MAIL RCPT DATA 2.0.0 RSET NOOP QUIT HELP VRFY 2.0.0 EXPN VERB ETRN DSN AUTH 2.0.0 STARTTLS 2.0.0 For more info use "HELP <topic>". 2.0.0 To report bugs in the implementation send email to 2.0.0 sendmail-bugs@sendmail.org. 2.0.0 For local information send email to Postmaster at your site. 2.0.0 End of HELP info
80/tcp    open  http        Apache httpd 1.3.33 ((Debian GNU/Linux))
|_http-title: Ph33r
|_http-server-header: Apache/1.3.33 (Debian GNU/Linux)
139/tcp   open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
199/tcp   open  smux        Linux SNMP multiplexer
445/tcp   open  netbios-ssn Samba smbd 3.0.14a-Debian (workgroup: WORKGROUP)
60000/tcp open  ssh         OpenSSH 3.8.1p1 Debian 8.sarge.6 (protocol 2.0)
| ssh-hostkey: 
|   1024 30:3e:a4:13:5f:9a:32:c0:8e:46:eb:26:b3:5e:ee:6d (DSA)
|_  1024 af:a2:49:3e:d8:f2:26:12:4a:a0:b5:ee:62:76:b0:18 (RSA)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: WAP|phone
Running (JUST GUESSING): Linux 2.4.X|2.6.X (94%), Sony Ericsson embedded (87%)
OS CPE: cpe:/o:linux:linux_kernel:2.4.20 cpe:/o:linux:linux_kernel:2.6.22 cpe:/h:sonyericsson:u8i_vivaz
Aggressive OS guesses: Tomato 1.28 (Linux 2.4.20) (94%), Tomato firmware (Linux 2.6.22) (94%), DD-WRT (Linux 2.4.36) (89%), Sony Ericsson U8i Vivaz mobile phone (87%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 4 hops
Service Info: OSs: Linux, Unix; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 5h59m57s, deviation: 2h49m47s, median: 3h59m53s
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.14a-Debian)
|   NetBIOS computer name: 
|   Workgroup: WORKGROUP\x00
|_  System time: 2024-06-20T19:25:19-04:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: share (dangerous)
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_smb2-time: Protocol negotiation failed (SMB2)
|_nbstat: NetBIOS name: 0XBABE, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
```

# initial/priv esc

```
sudo nmap -sSV -Pn -T4 -sCV -sU -p161 --script *snmp* 192.168.212.42     
```

```
PORT    STATE    SERVICE VERSION
161/tcp filtered snmp
161/udp open     snmp    Lexmark SNMP service; U.C. Davis, ECE Dept. Tom SNMPv3 server
| snmp-interfaces: 
|   lo
|     IP address: 127.0.0.1  Netmask: 255.0.0.0
|     Type: softwareLoopback  Speed: 10 Mbps
|     Status: up
|     Traffic stats: 0.00 Kb sent, 0.00 Kb received
|   eth0
|     IP address: 192.168.157.42  Netmask: 255.255.255.0
|     MAC address: 00:50:56:ab:1b:c8 (VMware)
|     Type: ethernetCsmacd  Speed: 100 Mbps
|     Status: up
|     Traffic stats: 12.13 Kb sent, 9.02 Kb received
|   sit0
|     MAC address: 00:00:00:00:1b:c8 (Xerox)
|     Type: tunnel  Speed: 0 Kbps
|     Status: down
|_    Traffic stats: 0.00 Kb sent, 0.00 Kb received
| snmp-info: 
|   enterprise: U.C. Davis, ECE Dept. Tom
|   engineIDFormat: unknown
|   engineIDData: 9e325869f30c7749
|   snmpEngineBoots: 60
|_  snmpEngineTime: 0s
| snmp-processes: 
|   1: 
|     Name: init
|     Path: init [2]
|   2: 
|     Name: ksoftirqd/0
|     Path: ksoftirqd/0
|   3: 
|     Name: events/0
|     Path: events/0
|   4: 
|     Name: khelper
|     Path: khelper
|   5: 
|     Name: kacpid
|     Path: kacpid
|   99: 
|     Name: kblockd/0
|     Path: kblockd/0
|   109: 
|     Name: pdflush
|     Path: pdflush
|   110: 
|     Name: pdflush
|     Path: pdflush
|   111: 
|     Name: kswapd0
|     Path: kswapd0
|   112: 
|     Name: aio/0
|     Path: aio/0
|   255: 
|     Name: kseriod
|     Path: kseriod
|   276: 
|     Name: scsi_eh_0
|     Path: scsi_eh_0
|   284: 
|     Name: khubd
|     Path: khubd
|   348: 
|     Name: shpchpd_event
|     Path: shpchpd_event
|   380: 
|     Name: kjournald
|     Path: kjournald
|   935: 
|     Name: vmmemctl
|     Path: vmmemctl
|   1177: 
|     Name: vmtoolsd
|     Path: /usr/sbin/vmtoolsd
|   3768: 
|     Name: syslogd
|     Path: /sbin/syslogd
|   3771: 
|     Name: klogd
|     Path: /sbin/klogd
|   3775: 
|     Name: clamd
|     Path: /usr/local/sbin/clamd
|   3778: 
|     Name: clamav-milter
|     Path: /usr/local/sbin/clamav-milter
|     Params: --black-hole-mode -l -o -q /var/run/clamav/clamav-milter.ctl
|   3787: 
|     Name: inetd
|     Path: /usr/sbin/inetd
|   3791: 
|     Name: nmbd
|     Path: /usr/sbin/nmbd
|     Params: -D
|   3793: 
|     Name: smbd
|     Path: /usr/sbin/smbd
|     Params: -D
|   3797: 
|     Name: snmpd
|     Path: /usr/sbin/snmpd
|     Params: -Lsd -Lf /dev/null -p /var/run/snmpd.pid
|   3799: 
|     Name: smbd
|     Path: /usr/sbin/smbd
|     Params: -D
|   3804: 
|     Name: sshd
|     Path: /usr/sbin/sshd
|   3882: 
|     Name: sendmail-mta
|     Path: sendmail: MTA: accepting connections
|   3896: 
|     Name: atd
|     Path: /usr/sbin/atd
|   3899: 
|     Name: cron
|     Path: /usr/sbin/cron
|   3906: 
|     Name: apache
|     Path: /usr/sbin/apache
|   3922: 
|     Name: getty
|     Path: /sbin/getty
|     Params: 38400 tty1
|   3928: 
|     Name: getty
|     Path: /sbin/getty
|     Params: 38400 tty2
|   3929: 
|     Name: getty
|     Path: /sbin/getty
|     Params: 38400 tty3
|   3930: 
|     Name: getty
|     Path: /sbin/getty
|     Params: 38400 tty4
|   3931: 
|     Name: getty
|     Path: /sbin/getty
|     Params: 38400 tty5
|   3932: 
|     Name: getty
|     Path: /sbin/getty
|     Params: 38400 tty6
|   3958: 
|     Name: apache
|     Path: /usr/sbin/apache
|   3959: 
|     Name: apache
|     Path: /usr/sbin/apache
|   3960: 
|     Name: apache
|     Path: /usr/sbin/apache
|   3961: 
|     Name: apache
|     Path: /usr/sbin/apache
|   3962: 
|     Name: apache
|_    Path: /usr/sbin/apache
| snmp-brute: 
|_  public - Valid credentials
| snmp-sysdescr: Linux 0xbabe.local 2.6.8-4-386 #1 Wed Feb 20 06:15:54 UTC 2008 i686
|_  System uptime: 3.64s (364 timeticks)
| snmp-netstat: 
|   TCP  0.0.0.0:25           0.0.0.0:0
|   TCP  0.0.0.0:80           0.0.0.0:0
|   TCP  0.0.0.0:139          0.0.0.0:0
|   TCP  0.0.0.0:199          0.0.0.0:0
|   TCP  0.0.0.0:445          0.0.0.0:0
|   UDP  0.0.0.0:137          *:*
|   UDP  0.0.0.0:138          *:*
|   UDP  0.0.0.0:161          *:*
|   UDP  42.157.168.192:137   *:*
|_  UDP  42.157.168.192:138   *:*
```

### exploit

https://www.exploit-db.com/exploits/4761

If you read the code, you’ll see that this exploit doesn’t provide direct code execution, but it opens port 31337 to give us a root `/bin/sh` shell. So, we just need to connect to it. As proof, you can use Nmap to check.

![[Pasted image 20240626150204.png]]

```
sudo nc -nv 192.168.157.42 31337
```

https://medium.com/@ardian.danny/oscp-practice-series-21-proving-grounds-clamav-774a396d568b