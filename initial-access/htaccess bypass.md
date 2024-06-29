basically config file which checks what to allow and what not

Below are some usage of htaccess files in server:

1) AUTHORIZATION, AUTHENTICATION: .htaccess files are often used to specify the security restrictions for the particular directory, hence the filename "access". The .htaccess file is often accompanied by an .htpasswd file which stores valid usernames and their passwords.

2) CUSTOMIZED ERROR RESPONSES: Changing the page that is shown when a server-side error occurs, for example HTTP 404 Not Found. Example : ErrorDocument 404 /notfound.html

3) REWRITING URLS: Servers often use .htaccess to rewrite "ugly" URLs to shorter and prettier ones.

4) CACHE CONTROL: .htaccess files allow a server to control User agent caching used by web browsers to reduce bandwidth usage, server load, and perceived lag.

https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-extension-blacklist-bypass

```
echo "AddType application/x-httpd-php .l33t" > .htaccess
```

filename parameter to .htaccess

Content-Type header to text/plain

now I can upload .l33t file