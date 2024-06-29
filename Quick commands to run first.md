

# upgrade exploitdb

```
sudo apt update && sudo apt install exploitdb
```

# redirection - CURL!

```
curl -v http://192.168.188.163
```

if it comes with `302` then add in hosts file!
# Nmap

### all basic port scan

```bash
sudo nmap -Pn -sS -T4 -oA enum/all_basic_port_scan $IP
```

### ports\_version\_scan

```bash
nmap -Pn -sCV -T4 -p$ports $IP -oN enum/ports_version_scan
```

### all ports

```bash
nmap -Pn -sCV -p- $IP -oN enum/all_ports
```

### UDP ports

```bash
nmap -sU -sV -vv $IP -oN enum/udp_ports
```

```
sudo nmap -Pn -n 192.168.182.71 -sU --top-ports=100
```
### subnet scanning

```bash
Nmap $IP/$subnet -sn
```

### ping sweep

```cmd
for /L %i in (1,1,255) do @ping -n 1 -w 200 192.168.1.%i > nul && echo 192.168.1.%i is up.
```

```powershell
1..20 | % {"192.168.1.$($_): $(Test-Connection -count 1 -comp 192.168.1.$($_) -quiet)"}
```

{% embed url="https://github.com/tedchen0001/OSCP-Notes/blob/master/find.md" %}

```
--script "vuln"
add in nmap

--script http-headers
--script-help http-headers

-p 139,445 --script smb-os-discovery
```

```
Test-NetConnection -Port 445 192.168.50.151

ComputerName     : 192.168.50.151
RemoteAddress    : 192.168.50.151
RemotePort       : 445
InterfaceAlias   : Ethernet0
SourceAddress    : 192.168.50.152
TcpTestSucceeded : True
```

# command to check which shell

```
(dir 2>&1 *`|echo CMD);&<# rem #>echo PowerShell
 ```




# dir busting

we can also make pattern for API like

pattern.txt
```
[GOBUSTER]/v1
[GOBUSTER]/v2
```
and then add -p to gobuster
### basic go buster
```
gobuster dir -u http://$IP/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,php3,html,txt,phtml,.asp,.aspx -t 75 | tee enum/gobuster_dir
```

### DNS mode

```bash
gobuster dns -d http://$IP/ -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-20000.txt -i –wildcard -t 75 | tee enum/gobuster_dns
```


### VHOST mode

```bash
gobuster vhost -v -u http://$IP/ -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt -k -t 75 | tee enum/gobuster_vhost
```

## FFUF

```bash
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-medium-words-lowercase.txt:FUZZ -u http://$IP/FUZZ -e .php,.html,.txt,phtml,.asp,.aspx | tee enum/ffuf_simple_dir
```

```bash
ffuf -u http://$IP/FUZZ  -w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-medium-words-lowercase.txt -recursion -recursion-depth 2 -e .php,.html,.txt,phtml,.asp,.aspx | tee enum/ffuf_recursive_dir
```


### subdomain

```bash
ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://FUZZ.domain.com/
```

### VHost

```bash
fuf -u http://domain.com -H 'Host: FUZZ.domain.com' -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt -ic -fs 1131 | tee enum/ffuf_subdomain
```

### username

```bash
ffuf -w /usr/share/wordlists/seclists/Usernames/top-usernames-shortlist.txt -X POST -d "username=FUZZ&&password=x" -H "Content-Type: application/x-www-form-urlencoded" -u http://mydomain.com/login -mr "username already exists" | tee enum/username_ffuf
```


\-mc , -fs

\-t for threads

### password

```bash
ffuf -w usernames.txt:W1,/usr/share/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 -X POST -d "username=W1&password=W2" -H "Content-Type: application/x-www-form-urlencoded" -u http://localhost:3000/login -fc 200 | tee enum/user_pass_ffuf
```





# Other Web Scanning

### nikto : headers

```bash
nikto -h $IP
```

### Netcat Banner Grabbing

```bash
nc $IP
nc -v $IP $port
```

### Telnet Banner Grabbing

```bash
telnet $IP $port
```

# Perl script for UDP ports

```perl
#!/usr/bin/perl
use strict;
use warnings;my @topPorts = (52, 67, 68, 69, 123, 135, 137, 138, 139, 161, 162, 445, 500, 514,520,631,1434,1900,4500,49152);
my $nc = "/bin/nc";
my $outFile;
my $target;if(@ARGV < 2) {
 print("Usage: $0 <target> <output file> \n");
 print("Example: perl $0 XXX.XXX.XXX.XXX udp_scan.txt \n");
        print("cat udp_scan.txt | grep -v \"?\" \n");
 exit(1);
}($target, $outFile) = @ARGV;
print("Performing Top 20 UDP Port Scan against: $target \n");
print("Output File: $outFile \n");open(FILE, ">", $target) or die("$!");
close(FILE);
print("Running: ");
foreach my $port (@topPorts) {
 open(NETCAT, "-|", "$nc -nv -u -z -w 1 $target $port 2>>$outFile") or die("$!");
 close(NETCAT);
 print(".");
}
print(" DONE!\n");


perl script.pl 10.10.10.10 udp_scan.txt; cat udp_scan.txt | grep -v "?"
```




# Setuid C program to compile

```c
#include <unistd.h>

int main()
{
    setuid(0);
    execl("/bin/bash", "bash", (char *)NULL);
    return 0;
}
```



