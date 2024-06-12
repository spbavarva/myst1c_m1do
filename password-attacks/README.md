# 🔑 Password attacks

## Windows Authentication Process

![[Auth_process1.webp]]

## LSASS

Base path

```
%SystemRoot%\System32\Lsass.exe
```

<table><thead><tr><th width="253">Authentication Packages</th><th width="486">Description</th></tr></thead><tbody><tr><td><code>Lsasrv.dll</code></td><td>The LSA Server service both enforces security policies and acts as the security package manager for the LSA. The LSA contains the Negotiate function, which selects either the NTLM or Kerberos protocol after determining which protocol is to be successful.</td></tr><tr><td><code>Msv1_0.dll</code></td><td>for local machine logons that don't require custom authentication.</td></tr><tr><td><code>Samsrv.dll</code></td><td>stores local security accounts, enforces locally stored policies, and supports APIs.</td></tr><tr><td><code>Kerberos.dll</code></td><td>Security package loaded by the LSA for Kerberos-based authentication on a machine.</td></tr><tr><td><code>Netlogon.dll</code></td><td>Network-based logon service.</td></tr><tr><td><code>Ntdsa.dll</code></td><td>used to create new records and folders in the Windows registry.</td></tr></tbody></table>

## SAM database

#### SYSKEY feature to secure offline software hacking

![[68747470733a2f2f61636164656d792e6861636b746865626f782e636f6d2f73746f726167652f6d6f64756c65732f3134372f617574686e5f637265646d616e5f6372656470726f762e706e67.webp]]
![Credential Manager](https://academy.hackthebox.com/storage/modules/147/authn\_credman\_credprov.png)
