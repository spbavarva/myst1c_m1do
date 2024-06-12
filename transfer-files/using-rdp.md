# Using RDP

Mounting a Linux Folder Using rdesktop:

{% code overflow="wrap" fullWidth="false" %}
```
rdesktop 10.10.10.132 -d HTB -u administrator -p 'Password0' -r disk:linux='/home/user/rdesktop/files'
```
{% endcode %}

Mounting a Linux Folder Using xfreerdp:

{% code overflow="wrap" %}
```
xfreerdp /v:10.10.10.132 /d:HTB /u:administrator /p:'Password' /drive:linux,/home/plaintext/htb/academy/filetransfer
```
{% endcode %}

To access the directory, we can connect to `\\tsclient\`, allowing us to transfer files to and from the RDP session.



in RDC (windows) After selecting the drive, we can interact with it in the remote session that follows.
