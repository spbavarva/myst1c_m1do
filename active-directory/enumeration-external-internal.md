
#### Identifying Hosts

1. Wireshark
2.  Tcpdump&#x20;

    ```shell-session
    sudo tcpdump -i ens224
    ```
3.  responder

    ```bash
    sudo responder -I ens224 -A
    ```
4.  fping

    ```shell-session
    fping -asgq 172.16.5.0/23
    ```



**Enumerating Users with Kerbrute**


```bash
kerbrute userenum -d INLANEFREIGHT.LOCAL --dc $IP ~/Desktop/jsmith.txt -o valid_ad_users
```
















### What Are We Looking For?

<table data-header-hidden><thead><tr><th width="261"></th><th></th></tr></thead><tbody><tr><td><strong>Data Point</strong></td><td><strong>Description</strong></td></tr><tr><td><code>IP Space</code></td><td>Valid ASN for our target, netblocks in use for the organization's public-facing infrastructure, cloud presence and the hosting providers, DNS record entries, etc.</td></tr><tr><td><code>Domain Information</code></td><td>Based on IP data, DNS, and site registrations. Who administers the domain? Are there any subdomains tied to our target? Are there any publicly accessible domain services present? (Mailservers, DNS, Websites, VPN portals, etc.) Can we determine what kind of defenses are in place? (SIEM, AV, IPS/IDS in use, etc.)</td></tr><tr><td><code>Schema Format</code></td><td>Can we discover the organization's email accounts, AD usernames, and even password policies? Anything that will give us information we can use to build a valid username list to test external-facing services for password spraying, credential stuffing, brute forcing, etc.</td></tr><tr><td><code>Data Disclosures</code></td><td>For data disclosures we will be looking for publicly accessible files ( .pdf, .ppt, .docx, .xlsx, etc. ) for any information that helps shed light on the target. For example, any published files that contain <code>intranet</code> site listings, user metadata, shares, or other critical software or hardware in the environment (credentials pushed to a public GitHub repo, the internal AD username format in the metadata of a PDF, for example.)</td></tr><tr><td><code>Breach Data</code></td><td>Any publicly released usernames, passwords, or other critical information that can help an attacker gain a foothold.</td></tr></tbody></table>

### Where/How Are We Looking?

<table data-header-hidden><thead><tr><th width="287"></th><th></th></tr></thead><tbody><tr><td><strong>Resource</strong></td><td><strong>Examples</strong></td></tr><tr><td><code>ASN / IP registrars</code></td><td><a href="https://www.iana.org/">IANA</a>, <a href="https://www.arin.net/">arin</a> for searching the Americas, <a href="https://www.ripe.net/">RIPE</a> for searching in Europe, <a href="https://bgp.he.net/">BGP Toolkit</a></td></tr><tr><td><code>Domain Registrars &#x26; DNS</code></td><td><a href="https://www.domaintools.com/">Domaintools</a>, <a href="http://ptrarchive.com/">PTRArchive</a>, <a href="https://lookup.icann.org/lookup">ICANN</a>, manual DNS record requests against the domain in question or against well known DNS servers, such as <code>8.8.8.8</code>.</td></tr><tr><td><code>Social Media</code></td><td>Searching Linkedin, Twitter, Facebook, your region's major social media sites, news articles, and any relevant info you can find about the organization.</td></tr><tr><td><code>Public-Facing Company Websites</code></td><td>Often, the public website for a corporation will have relevant info embedded. News articles, embedded documents, and the "About Us" and "Contact Us" pages can also be gold mines.</td></tr><tr><td><code>Cloud &#x26; Dev Storage Spaces</code></td><td><a href="https://github.com/">GitHub</a>, <a href="https://grayhatwarfare.com/">AWS S3 buckets &#x26; Azure Blog storage containers</a>, <a href="https://www.exploit-db.com/google-hacking-database">Google searches using "Dorks"</a></td></tr><tr><td><code>Breach Data Sources</code></td><td><a href="https://haveibeenpwned.com/">HaveIBeenPwned</a> to determine if any corporate email accounts appear in public breach data, <a href="https://www.dehashed.com/">Dehashed</a> to search for corporate emails with cleartext passwords or hashes we can try to crack offline. We can then try these passwords against any exposed login portals (Citrix, RDS, OWA, 0365, VPN, VMware Horizon, custom applications, etc.) that may use AD authentication.</td></tr></tbody></table>

The `BGP-Toolkit` hosted by [Hurricane Electric](http://he.net/) is a fantastic resource for researching what address blocks are assigned to an organization and what ASN they reside within

For DNS sites like [domaintools](https://whois.domaintools.com/), and [viewdns.info](https://viewdns.info/) (prefer) are great spots to start.

```shell-session
nslookup ns1.inlanefreight.com
```

[Dehashed](http://dehashed.com/) is an excellent tool for hunting for cleartext credentials and password hashes in breach data

```shell-session
sudo python3 dehashed.py -q inlanefreight.local -p
```
