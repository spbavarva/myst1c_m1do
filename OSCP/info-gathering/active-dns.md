# Active - DNS

{% embed url="https://app.gitbook.com/o/oD7jvP4yyK9fTXdngiwb/s/ROk2K3zZId4eElllNbe7/initial-access/dns-53" %}

whenever DNS is available as TCP, high chance that it has zone transfer

```
host www.megacorpone.com

host -t mx megacorpone.com
host -t txt megacorpone.com


cat list.txt
www
ftp
mail
owa
proxy
router

for ip in $(cat list.txt); do host $ip.megacorpone.com; done


dnsrecon -d megacorpone.com -t std
dnsrecon -d megacorpone.com -D ~/list.txt -t brt


dnsenum megacorpone.com


dig any <domain.tld> @
dig axfr domain.tld @IP
dig axfr @ns.domain.tld domain.tld
```

![[image 39.png]]

after connecting to windows cmd
```
nslookup mail.megacorptwo.com
nslookup -type=TXT info.megacorptwo.com 192.168.50.151
```

