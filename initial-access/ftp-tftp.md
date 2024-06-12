# FTP/TFTP

### FTP Modes

FTP operates in two distinct modes:

1. **Active Mode**
   * firewalls may block external connections initiated by the server.
2. Passive Mode
   * connection made to the server

* it is a clear-text protocol
* use TCP

***

### Download all files at once:

To download all files at once from an FTP server, especially when dealing with servers that don't support passive mode well or when passive mode is specifically not desired,&#x20;

```markdown
To download all files at once in active mode, use the following command:
```

```bash
wget -m --no-passive ftp://anonymous:anonymous@SERVER_IP
```

This approach is particularly helpful when you're dealing with firewalls that may block the connections initiated by the server, which is a common issue in active FTP sessions.

***

#### TFTP (Trivial File Transfer Protocol)

* Use UDP&#x20;
* Does not require user auth, best suited for local and protected networks.
* Lacks support for directories; files are transferred to and from a fixed location.

***

#### **vsFTPd (Very Secure FTP Daemon)**

* Most used FTP server
* Configuration file: `/etc/vsftpd.conf`
* User deny list: `/etc/ftpusers`
