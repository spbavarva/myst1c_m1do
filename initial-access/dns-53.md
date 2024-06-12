# DNS - 53

#### DNS Record Types

<table><thead><tr><th width="218">DNS Record</th><th>Description</th></tr></thead><tbody><tr><td>A</td><td>Returns an IPv4</td></tr><tr><td>AAAA</td><td>Returns an IPv6</td></tr><tr><td>MX</td><td>Returns mail servers</td></tr><tr><td>NS</td><td>Returns DNS (nameservers) of the domain</td></tr><tr><td>TXT</td><td>This record can contain various information</td></tr><tr><td>CNAME</td><td>This record serves as an alias. If the domain <a href="http://www.hackthebox.eu">www.hackthebox.eu</a> should point to the same IP, and we create an A record for one and a CNAME record for the other.</td></tr><tr><td>PTR</td><td>The PTR record works (reverse lookup). It converts IP addresses into valid domain names.</td></tr><tr><td>SOA</td><td>Provides information of the DNS zone and email address of the administrative contact.</td></tr></tbody></table>



| Command                                                                                           | Description                              |
| ------------------------------------------------------------------------------------------------- | ---------------------------------------- |
| dig ns inlanefreight.htb @$IP                                                                     | NS request to the specific nameserver.   |
| dig any \<domain.tld> @                                                                           | ANY request to the specific nameserver.  |
| dig axfr \<domain.tld> @                                                                          | AXFR request to the specific nameserver. |
| dnsenum --dnsserver --enum -p 0 -s 0 -o found\_subdomains.txt -f \~/subdomains.list \<domain.tld> | Subdomain brute forcing.                 |

```
dig -x <target ip> @<dns_ip>
```

website

```
https://crt.sh/
```

{% embed url="https://app.gitbook.com/o/oD7jvP4yyK9fTXdngiwb/s/ROk2K3zZId4eElllNbe7/oscp/info-gathering/active" %}

> **use subdomains also while doing any/axfr. zones often have subdomain names**
