Linux ttl=61

# scan

```
PORT   STATE SERVICE VERSION                                       
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0) 
| ssh-hostkey:                                                     
|   3072 98:4e:5d:e1:e6:97:29:6f:d9:e0:d4:82:a8:f6:4f:3f (RSA)      
|   256 57:23:57:1f:fd:77:06:be:25:66:61:14:6d:ae:5e:98 (ECDSA)     
|_  256 c7:9b:aa:d5:a6:33:35:91:34:1e:ef:cf:61:a8:30:1c (ED25519)    
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))                 
|_http-server-header: Apache/2.4.41 (Ubuntu)          
```

# initial

got `/filemanager` directory with gobuster

tried default creds `admin:admin` and it worked

![[Pasted image 20240628195500.png]]

https://github.com/cowsecurity/CVE-2023-27842

I thought I need to look for other exploits but no, we can upload our shell there

so I uploaded but couldn't execute that

so need to think differently

#cancallshellwithcurl

**YES** I forget that I can use `curl` to call my shell!!

```
curl http://192.168.182.16/filemanager/best_shell.php
```

and got www-data shell

which was quite useless

so dig more into all files and shits and got hash for `dora`

![[Pasted image 20240628195735.png]]

used john to crack it

![[Pasted image 20240628195842.png]]

# priv esc

checked everything but no luck!!

my manual ways got out of hands!

then looked at groups which will show with `id`

![[Pasted image 20240628200010.png]]

https://book.hacktricks.xyz/linux-hardening/privilege-escalation/interesting-groups-linux-pe

https://vk9-sec.com/disk-group-privilege-escalation/

### Disk Group

From id command result, we notice Dora belong to disk group. By googling “6(disk) privilege escalation” , we found this useful link that shows how we can get privilege escalation while the user belongs to disk group. From the hacktrick link, we can use debugfs to enumerate disk with root level privileges where we can read and write to any files.

First, find where / is mounted. / is root directory, why / ? because we want to access the root folder.

```
df -h
```

When you run df -h, you'll see a list of mounted filesystems along with information about their total size, used space, available space, and usage percentage.

![[Pasted image 20240628201215.png]]

We discover the `/` at `/dev/mapper/ubuntu — vg-ubuntu — lv`

```
debugfs /dev/mapper/ubuntu-vg-ubuntu-lv
```

once we are inside, take ssh keys but we have no luck

try our luck with `/etc/passwd`

but even also we can read proof.txt 😉

![[Pasted image 20240628201719.png]]

we can crack this hash and `su` to root~

https://medium.com/@0xrave/extplorer-proving-grounds-practice-walkthrough-73c076002709