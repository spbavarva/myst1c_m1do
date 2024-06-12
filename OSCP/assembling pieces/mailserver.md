
## rustscan
```
PORT      STATE SERVICE      REASON
25/tcp    open  smtp         syn-ack ttl 125
80/tcp    open  http         syn-ack ttl 125
110/tcp   open  pop3         syn-ack ttl 125
135/tcp   open  msrpc        syn-ack ttl 125
139/tcp   open  netbios-ssn  syn-ack ttl 125
143/tcp   open  imap         syn-ack ttl 125
445/tcp   open  microsoft-ds syn-ack ttl 125
587/tcp   open  submission   syn-ack ttl 125
5985/tcp  open  wsman        syn-ack ttl 125
47001/tcp open  winrm        syn-ack ttl 125
49664/tcp open  unknown      syn-ack ttl 125
49665/tcp open  unknown      syn-ack ttl 125
49666/tcp open  unknown      syn-ack ttl 125
49667/tcp open  unknown      syn-ack ttl 125
49668/tcp open  unknown      syn-ack ttl 125
49669/tcp open  unknown      syn-ack ttl 125
49670/tcp open  unknown      syn-ack ttl 125

```


## nmap
```
sudo nmap -sS -Pn -T4 -sCV -O $IP1 -oA mailsrv1/full.nmap    
```

```
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-05-22 07:12 EDT
Nmap scan report for 192.168.241.242
Host is up (0.078s latency).
Not shown: 992 closed tcp ports (reset)
PORT    STATE SERVICE       VERSION
25/tcp  open  smtp          hMailServer smtpd
| smtp-commands: MAILSRV1, SIZE 20480000, AUTH LOGIN, HELP
|_ 211 DATA HELO EHLO MAIL NOOP QUIT RCPT RSET SAML TURN VRFY
80/tcp  open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows Server
110/tcp open  pop3          hMailServer pop3d
|_pop3-capabilities: USER TOP UIDL
135/tcp open  msrpc         Microsoft Windows RPC
139/tcp open  netbios-ssn   Microsoft Windows netbios-ssn
143/tcp open  imap          hMailServer imapd
|_imap-capabilities: CHILDREN IMAP4 IDLE NAMESPACE SORT IMAP4rev1 CAPABILITY OK completed RIGHTS=texkA0001 ACL QUOTA
445/tcp open  microsoft-ds?
587/tcp open  smtp          hMailServer smtpd
| smtp-commands: MAILSRV1, SIZE 20480000, AUTH LOGIN, HELP
|_ 211 DATA HELO EHLO MAIL NOOP QUIT RCPT RSET SAML TURN VRFY
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=5/22%OT=25%CT=1%CU=44671%PV=Y%DS=4%DC=I%G=Y%TM=664D
OS:D32E%P=x86_64-pc-linux-gnu)SEQ(SP=FF%GCD=1%ISR=10D%TI=I%TS=A)OPS(O1=M551
OS:NW8ST11%O2=M551NW8ST11%O3=M551NW8NNT11%O4=M551NW8ST11%O5=M551NW8ST11%O6=
OS:M551ST11)WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FFDC)ECN(R=Y%DF=
OS:Y%T=80%W=FFFF%O=M551NW8NNS%CC=Y%Q=)T1(R=Y%DF=Y%T=80%S=O%A=S+%F=AS%RD=0%Q
OS:=)T2(R=N)T3(R=N)T4(R=N)T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(
OS:R=N)T7(R=N)U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=80BD%
OS:RUD=G)IE(R=N)

Network Distance: 4 hops
Service Info: Host: MAILSRV1; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-05-22T11:12:39
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 45.37 seconds

```